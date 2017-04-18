<properties
    pageTitle="Ligar a uma máquina de Virtual do SQL Server (clássico) | Microsoft Azure"
    description="Saiba como ligar ao SQL Server em execução numa máquina Virtual no Azure. Este tópico utiliza o modelo de implementação clássica. Os cenários diferem consoante a configuração de rede e a localização do cliente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Ligar a uma máquina de Virtual do SQL Server no Azure (implementação clássico)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-connect.md)
- [Clássico](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Descrição geral

Este tópico descreve como ligar à sua instância do SQL Server em execução numa máquina virtual Azure. -Abrange alguns [cenários de conectividade gerais](#connection-scenarios) e, em seguida, fornece [passos detalhados para configurar a conectividade do SQL Server numa VM Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Se estiver a utilizar o Gestor de recursos VMs, consulte o artigo [ligar a uma SQL Server Máquina Virtual no Azure utilizando o Gestor de recursos](virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Cenários de ligação

A forma como um cliente liga-se para o SQL Server em execução numa máquina Virtual difere consoante a localização do cliente e a configuração de máquina/funcionamento em rede. Estes cenários incluem:

- [Ligar ao SQL Server no serviço na nuvem mesmo](#connect-to-sql-server-in-the-same-cloud-service)
- [Ligar ao SQL Server através da internet](#connect-to-sql-server-over-the-internet)
- [Ligar ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

>[AZURE.NOTE] Antes de ligar com qualquer um dos seguintes métodos, tem de seguir [os passos neste artigo para configurar a conectividade](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Ligar ao SQL Server no serviço na nuvem mesmo

Várias máquinas virtuais podem ser criadas no serviço de nuvem do mesmo. Para compreender este cenário máquinas virtuais, consulte o artigo [como ligar máquinas virtuais com um serviço de rede ou na nuvem virtual](virtual-machines-windows-classic-connect-vms.md#connect-vms-in-a-standalone-cloud-service). Este cenário é quando um cliente numa máquina virtual tenta ligar para o SQL Server em execução noutro computador virtual no serviço de nuvem do mesmo.

Neste cenário, pode ligar utilizando o **nome** VM (também mostrado como **Nome do computador** ou o **nome do anfitrião** no portal do). Este é o nome que forneceu para a VM durante a criação. Por exemplo, se chamar o SQL VM **mysqlvm**, um cliente VM no serviço na nuvem mesmo pode utilizar a cadeia de ligação seguinte para ligar:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Ligar ao SQL Server através da Internet

Se pretender ligar ao seu motor de base de dados do SQL Server a partir da Internet, terá de criar um ponto final de máquina virtual para a comunicação de TCP recebida. Este passo da configuração Azure, encaminha o tráfego de portas TCP recebido para uma porta TCP que esteja acessível para a máquina virtual.

Para ligar através da internet, tem de utilizar o nome de DNS a VM e o número da porta VM ponto final (configurado neste artigo). Para localizar o nome de DNS, navegue até ao Portal do Azure e selecione **máquinas virtuais (clássico)**. Em seguida, selecione o seu máquina virtual. O **nome de DNS** é apresentada na secção **Overview** .

Por exemplo, considere uma máquina virtual clássica denominada **mysqlvm** com um nome de DNS de **mysqlvm7777.cloudapp.net** e um ponto final de VM de **57500**. Partindo do princípio de conectividade adequadamente configurada, a cadeia de ligação seguinte pode ser utilizada para aceder a máquina virtual a partir de qualquer lugar na internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Apesar de esta permite conectividade para clientes através da internet, isto não implica que qualquer pessoa pode ligar-se para o SQL Server. Fora dos clientes têm correto nome de utilizador e palavra-passe. Para segurança adicional, não utilize a conhecida porta 1433 para o ponto final de máquina virtual público. E se possível, considere a adição de uma ACL no seu ponto final para restringir o tráfego apenas para os clientes tenha atribuído permissão. Para obter instruções sobre como utilizar juntamente com os pontos finais, consulte o artigo [Gerir a ACL num ponto final](virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

>[AZURE.NOTE] É importante ter em atenção que quando utiliza esta técnica para comunicar com o SQL Server, todos os dados de saída do Centro de dados Azure está sujeito a normal [preços no transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Ligar ao SQL Server na mesma rede virtual

[Rede virtual](../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Pode ligar VMs na mesma rede virtual, mesmo que existam essas VMs em serviços em nuvem diferente. E com uma [VPN do site para o site](../vpn-gateway/vpn-gateway-site-to-site-create.md), pode criar uma arquitetura de implementações que liga VMs com redes no local e máquinas.

Redes virtuais também permite-lhe aderir a sua VMs Azure para um domínio. Este é a única forma de utilizar a autenticação do Windows para o SQL Server. Os cenários de ligações exigem autenticação do SQL com nomes de utilizador e palavras-passe.

Se passar para configurar um ambiente do domínio e a autenticação do Windows, não terá de utilizar os passos neste artigo para configurar o ponto final público ou a autenticação do SQL e inícios de sessão. Neste cenário, pode ligar a sua instância do SQL Server ao especificar o nome do SQL Server VM na cadeia de ligação. O exemplo seguinte assume que também tenha sido configurada autenticação do Windows e foi concedido acesso a instância do SQL Server ao utilizador.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Passos para configurar a conectividade de SQL Server numa VM Azure

Os passos seguintes demonstram como ligar a instância do SQL Server na Internet com SQL Server Management Studio (SSMS). No entanto, os mesmos passos aplicam-se para tornar o seu máquina de virtual do SQL Server acessível para as suas aplicações, executar ambas no local e no Azure.

Antes de poder ligar para a instância do SQL Server a partir de outro VM ou na internet, tem de concluir as seguintes tarefas, tal como descrito nas secções que se seguem:

- [Criar um ponto final TCP para a máquina virtual](#create-a-tcp-endpoint-for-the-virtual-machine)
- [Portas TCP abertas na firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurar o SQL Server para escutar o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurar o SQL Server para a autenticação de modo misto](#configure-sql-server-for-mixed-mode-authentication)
- [Criar inícios de sessão do SQL Server autenticação](#create-sql-server-authentication-logins)
- [Determinar o nome DNS da máquina virtual](#determine-the-dns-name-of-the-virtual-machine)
- [Ligar para o motor de base de dados a partir de outro computador](#connect-to-the-database-engine-from-another-computer)

O caminho da ligação é resumido pelo diagrama seguinte:

![Ligar a uma máquina de virtual do SQL Server](../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Classic Steps](../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Próximos passos

Se também estiver a planear utilizar grupos de Disponibilidade AlwaysOn para elevada disponibilidade e recuperação de falhas, deverá tomar em consideração de execução de uma escuta. Os clientes de base de dados ligar para o serviço de escuta em vez de diretamente para um dos instâncias do SQL Server. A escuta encaminha os clientes para a réplica principal no grupo disponibilidade. Para mais informações, consulte o artigo [Configurar uma escuta ILB para grupos de Disponibilidade AlwaysOn no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

É importante rever todas as melhores práticas de segurança para SQL Server em execução numa máquina virtual Azure. Para mais informações, consulte o artigo [Considerações de segurança para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-security.md).

[Explorar o caminho de formação](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais Azure. 

Para obter outros tópicos relacionados com a execução do SQL Server no Azure VMs, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
