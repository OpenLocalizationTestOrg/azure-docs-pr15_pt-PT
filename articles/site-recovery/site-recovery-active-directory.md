<properties
    pageTitle="Proteger o Active Directory e DNS com a recuperação de Azure Site | Microsoft Azure"
    description="Este artigo descreve como implementar uma solução de recuperação de falhas do Active Directory utilizando Azure a recuperação do Site."
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="08/31/2016"
    ms.author="pratshar"/>

# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteger o Active Directory e DNS com a recuperação de Azure Site

Aplicações empresariais, como o SharePoint, Dynamics AX e SAP dependem do Active Directory e uma infraestrutura DNS para funcionar corretamente. Quando cria uma solução de recuperação de falhas para as aplicações, é importante lembrar-se de que precisa de proteger e recuperar Active Directory e DNS antes dos outros componentes de aplicação, para se certificar de que coisas funcionam corretamente quando ocorre falhas.

Recuperação de sites é um serviço Azure que fornece recuperação de falhas por orquestrar replicação, activação pós-falha e recuperação de máquinas virtuais. Recuperação de sites suporta um número de cenários de replicação para proteger de forma consistente e forma totalmente integrada activação pós-falha de máquinas virtuais e aplicações para nuvens depare, público ou privado.

Utilizar recuperação de sites, pode criar um plano de recuperação de falhas automatizado completa para o Active Directory. Quando ocorrem interrupções, pode iniciar uma activação pós-falha em segundos a partir de qualquer lugar e começar do Active Directory a trabalhar em alguns minutos. Se tiver implementado do Active Directory para várias aplicações como o SharePoint e SAP no seu site principal e pretende falhar sobre o site completo, pode falhar ao longo do Active Directory pela primeira vez utilizando a recuperação de Site e, em seguida, falhar sobre as outras aplicações que utilizam os planos de recuperação de específicos da aplicação.

Este artigo explica como criar uma solução de recuperação de falhas para Active Directory, como efetuar planeadas, não planeado e activações pós-falha de teste com um plano de recuperação com um clique, as configurações suportadas e pré-requisitos.  Deve estar familiarizado com o Active Directory e recuperação de sites do Azure antes de começar.

Existem duas opções recomendadas, com base na complexidade do seu ambiente.

### <a name="option-1"></a>Opção 1

Se tiver um pequeno número de aplicações e um único controlador de domínio e pretende falhar ao longo de todo o site, em seguida, recomendamos que utilize recuperação de sites para criar uma réplica o controlador de domínio para o site secundário (se está a falhar sobre para Azure ou para um site secundário). A mesma máquina virtual replicada podem ser utilizada para teste activação pós-falha demasiado.

### <a name="option-2"></a>Opção 2

Se tiver um grande número de aplicações e existe mais do que um controlador de domínio no ambiente ou, se planeia falhar sobre algumas aplicações de cada vez, recomendamos que para além de replicadas a máquina de virtual do controlador de domínio com a recuperação de Site irá também de configurar um controlador de domínio adicionais no site de destino (Azure ou um centro de dados secundária no local).

>[AZURE.NOTE] Mesmo se estiver a execução opção-2, para fazer um teste activação pós-falha que ainda terá de criar uma réplica o controlador de domínio utilizando a recuperação do Site. Leia a [Testar activação pós-falha considerações](#considerations-for-test-failover) para obter mais informações.


As secções seguintes explicam como ativar proteção para um controlador de domínio no Site de recuperação e como configurar um controlador de domínio no Azure.


## <a name="prerequisites"></a>Pré-requisitos

- Uma implementação no local do Active Directory e servidor de DNS.
- Um cofre dos serviços de recuperação do Azure Site numa subscrição do Microsoft Azure.
- Se estiver a replicadas para o Azure executar a ferramenta de avaliação do Azure Máquina Virtual preparação no VMs para garantir que está compatíveis com o Azure VMs e dos serviços de recuperação do Azure Site.


## <a name="enable-protection-using-site-recovery"></a>Activar a protecção utilizando a recuperação de Site


### <a name="protect-the-virtual-machine"></a>Proteger a máquina virtual

Ative proteção da máquina de virtual DNS do controlador de domínio da recuperação de sites. Configure definições de recuperação de sites com base no tipo de máquina virtual (Hyper-V ou VMware). Recomendamos que a frequência de replicação consistentes uma falha de 15 minutos.

###<a name="configure-virtual-machine-network-settings"></a>Configurar definições de rede de máquina virtual

Para o computador virtual do DNS do controlador de domínio, configure definições de rede recuperação de sites para que a VM será anexada à rede de à direita após activação pós-falha. Por exemplo, se estiver a replicação VMs Hyper-V para Azure pode selecionar a VM na nuvem VMM ou no grupo de proteção para configurar as definições de rede, conforme apresentado abaixo

![Definições de rede VM](./media/site-recovery-active-directory/VM-Network-Settings.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteger o Active Directory com replicação do Active Directory

### <a name="site-to-site-protection"></a>Proteção de site para o site

Criar um controlador de domínio no site secundário e especifique o nome do mesmo domínio que está a ser utilizado no site principal quando promover o servidor para uma função de controlador de domínio. Pode utilizar o snap-in **locais do Active Directory e serviços** para configurar as definições no objeto de ligação de site ao qual são adicionados os sites. Ao configurar as definições de uma ligação de sites, pode controlar quando ocorre a replicação entre dois ou mais locais e com que frequência. Consulte o artigo [Agendar replicação entre locais](https://technet.microsoft.com/library/cc731862.aspx) para obter mais detalhes.

###<a name="site-to-azure-protection"></a>Proteção de site para Azure

Siga as instruções para [criar um controlador de domínio numa rede virtual Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Quando o servidor para uma função de controlador de domínio promove Especifica o mesmo nome de domínio que é utilizado no site principal.

Em seguida, [reconfigurar o servidor DNS para a rede virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), para utilizar o servidor DNS no Azure.

![Rede Azure](./media/site-recovery-active-directory/azure-network.png)

## <a name="test-failover-considerations"></a>Considerações de activação pós-falha de teste

Teste activação pós-falha ocorre numa rede que tenha isoladas da rede de produção, de modo a que não haja nenhuma impacto no cargas de trabalho de produção.

A maioria das aplicações exigir também a presença de um controlador de domínio e um servidor de DNS para funcionar, para que antes da aplicação Ocorreu uma falha ao longo do, um controlador de domínio tem de ser criada na rede isolada para ser utilizado para activação pós-falha de teste. A forma mais fácil para fazer isto é ativar proteção na máquina domínio DNS do controlador virtual com a recuperação de Site e executa um teste activação pós-falha de que a máquina virtual, antes de executar um teste activação pós-falha do plano de recuperação para a aplicação. Eis como fazê-lo:

1. Ative proteção no recuperação de Site para a máquina de virtual do DNS do controlador de domínio.
2. Crie uma rede isolada. Qualquer rede virtual criado por predefinição no Azure está isolada de outras redes. Recomendamos que o intervalo de endereços IP para esta rede é o mesmo que a sua rede de produção. Não Active a conectividade de site para o site desta rede.
3. Fornece um endereço IP do DNS na rede criada, como o endereço IP que esperava máquina virtual DNS para obter. Se estiver a replicação para Azure, em seguida, forneça o endereço IP para a VM que será utilizada no activação pós-falha na definição de **IP de destino** nas propriedades VM. Se estiver a ser replicadas para outro no local site e estiver a utilizar o seguimento DHCP as instruções para [Configurar DNS e DHCP para activação pós-falha de teste](site-recovery-failover.md#prepare-dhcp)

>[AZURE.NOTE] O endereço IP atribuído a uma máquina virtual durante um teste activação pós-falha é o mesmo que o endereço IP obteria durante uma falha na ligação planeada ou não planeada, se o endereço IP estiver disponível na rede de activação pós-falha de teste. Se não estiver, a máquina virtual recebe um endereço IP diferente que está disponível na rede de activação pós-falha de teste.

4. Na máquina de virtual do controlador de domínio executar um teste activação pós-falha do mesmo na rede isolada. Utilize o ponto de recuperação consistentes de aplicações disponíveis mais recente da máquina de virtual do controlador de domínio para efetuar a activação de teste pós-falha. 
5. Execute um teste activação pós-falha para o plano de recuperação de aplicação.
6. Depois de a testar estiver concluída, marca a tarefa de activação pós-falha de teste de máquina de virtual do controlador de domínio e do plano de recuperação 'Concluída' no separador **tarefas** no portal do recuperação de sites.

### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em computadores diferentes

Se o DNS não estiver na mesma máquina virtual como o controlador de domínio terá de criar uma VM de DNS para o teste activação pós-falha. Se estiverem na mesma VM, pode ignorar esta secção.

Pode utilizar um servidor DNS fresco e criar todas as zonas. Por exemplo, se o seu domínio do Active Directory for contoso.com, pode criar uma zona de DNS com o nome contoso.com. As entradas correspondente ao Active Directory têm de ser atualizadas em DNS, da seguinte forma:

1. Certifique-se de que estas definições estão no local antes de outros máquina virtual no plano de recuperação surgir:

    - A zona deve ter o nome depois do nome de raiz floresta.
    - A zona tem de ser ficheiro cópia.
    - A zona tem de estar ativada para atualizações seguras e não seguro.
    - A resolução da máquina de virtual do controlador de domínio deve apontar para o endereço IP da máquina virtual DNS.

2. Execute o seguinte comando na máquina de virtual do controlador de domínio:

    `nltest /dsregdns`

3. Adicionar uma zona no servidor de DNS, permitir atualizações não seguro e adicionar uma entrada para a mesma ao DNS:

        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1


## <a name="next-steps"></a>Próximos passos

Leitura [das cargas de trabalho que posso proteger?](../site-recovery/site-recovery-workload.md) para saber mais sobre como proteger das cargas de trabalho de empresa com Azure recuperação de Site.
