<properties
    pageTitle="Ligar a uma máquina de Virtual do SQL Server (Gestor de recurso) | Microsoft Azure"
    description="Saiba como ligar ao SQL Server em execução numa máquina Virtual no Azure. Este tópico utiliza o modelo de implementação clássica. Os cenários diferem consoante a configuração de rede e a localização do cliente."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"    
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="connect-to-a-sql-server-virtual-machine-on-azure-resource-manager"></a>Ligar a uma máquina de Virtual do SQL Server no Azure (Gestor de recurso)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-connect.md)
- [Clássico](virtual-machines-windows-classic-sql-connect.md)

## <a name="overview"></a>Descrição geral

Este tópico descreve como ligar à sua instância do SQL Server em execução numa máquina virtual Azure. -Abrange alguns [cenários de conectividade gerais](#connection-scenarios) e, em seguida, fornece [passos detalhados para configurar a conectividade do SQL Server numa VM Azure](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica. Para ver a versão clássica deste artigo, consulte o artigo [ligar a uma SQL Server Máquina Virtual no Azure clássica](virtual-machines-windows-classic-sql-connect.md).

Se preferir que um guia passo a passo completa de aprovisionamento e conectividade, consulte o artigo [aprovisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Cenários de ligação

A forma como um cliente liga-se para o SQL Server em execução numa máquina Virtual difere consoante a localização do cliente e a configuração de máquina/funcionamento em rede. Estes cenários incluem:

- [Ligar ao SQL Server através da internet](#connect-to-sql-server-over-the-internet)
- [Ligar ao SQL Server na mesma rede virtual](#connect-to-sql-server-in-the-same-virtual-network)

### <a name="connect-to-sql-server-over-the-internet"></a>Ligar ao SQL Server através da Internet

Se pretender ligar ao seu motor de base de dados do SQL Server a partir da Internet, existem vários passos necessários, como configurar a firewall, permitindo-autenticação do SQL e configurar o seu grupo de segurança de rede tem de ter uma regra de grupo de segurança de rede para permitir o tráfego do TCP no porta 1433.

Se utilizar o portal de aprovisionamento de uma imagem de máquina virtual do SQL Server com o Gestor de recursos, estes passos são feitos por si quando seleciona **público** para a opção de conectividade SQL:

![Opção de conectividade SQL pública durante o aprovisionamento](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Se este não tiver sido um durante o aprovisionamento, em seguida, pode configurar manualmente o SQL Server e máquinas virtuais ao seguir [os passos neste artigo para configurar manualmente a conectividade](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm).

>[AZURE.NOTE] A imagem de máquina virtual do SQL Server Express edition não ativar o protocolo TCP/IP automaticamente. Para Express edition, tem de utilizar o Gestor de configuração do SQL Server para [Ativar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a VM.

Quando este estiver concluída, qualquer cliente com acesso à internet pode ligar a instância do SQL Server ao especificar o endereço IP público da máquina virtual ou a etiqueta DNS atribuídos a esse endereço IP. Se a porta do servidor de SQL 1433, não é necessário especificá-la na cadeia de ligação.

    "Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Apesar de esta permite conectividade para clientes através da internet, isto não implica que qualquer pessoa pode ligar-se para o SQL Server. Fora dos clientes têm correto nome de utilizador e palavra-passe. Para segurança adicional, pode evitar a conhecida porta 1433. Por exemplo, se configurado o SQL Server para escutar a porta 1500 e estabelecida firewall inicial e as regras de grupo de segurança de rede, pode ligar ao acrescentar o número da porta para o nome do servidor tal como no exemplo seguinte:

    "Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

>[AZURE.NOTE] É importante ter em atenção que quando utiliza esta técnica para comunicar com o SQL Server, todos os dados de saída do Centro de dados Azure está sujeito a normal [preços no transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Ligar ao SQL Server na mesma rede virtual

[Rede virtual](../virtual-network/virtual-networks-overview.md) permite cenários adicionais. Pode ligar VMs na mesma rede virtual, mesmo que existam essas VMs em diferentes grupos de recursos. E com uma [VPN do site para o site](../vpn-gateway/vpn-gateway-site-to-site-create.md), pode criar uma arquitetura de híbrido que liga VMs com redes no local e máquinas.

Redes virtuais também permite-lhe aderir a sua VMs Azure para um domínio. Este é a única forma de utilizar a autenticação do Windows para o SQL Server. Os cenários de ligações exigem autenticação do SQL com nomes de utilizador e palavras-passe.

Se utilizar o portal de aprovisionamento de uma imagem de máquina virtual do SQL Server com o Gestor de recursos, as regras de firewall adequada para a comunicação da rede virtual são configuração quando seleciona **privado** para a opção de conectividade SQL. Se este não tiver sido um durante o aprovisionamento, em seguida, pode configurar manualmente o SQL Server e máquinas virtuais ao seguir [os passos neste artigo para configurar manualmente a conectividade](#steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm). Mas, se estiver a planear configurar um ambiente do domínio e a autenticação do Windows, não terá de utilizar os passos neste artigo para configurar a autenticação do SQL e inícios de sessão. Também não tem de configurar regras de grupo de segurança de rede para o access através da internet.

>[AZURE.NOTE] A imagem de máquina virtual do SQL Server Express edition não ativar o protocolo TCP/IP automaticamente. Para Express edition, tem de utilizar o Gestor de configuração do SQL Server para [Ativar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a VM.

Partindo do princípio que tiver configurado o DNS na sua rede virtual, pode ligar-se a sua instância do SQL Server ao especificar o nome do computador SQL Server VM na cadeia de ligação. O exemplo seguinte também assume que também tenha sido configurada autenticação do Windows e foi concedido acesso a instância do SQL Server ao utilizador.

    "Server=mysqlvm;Integrated Security=true"

Tenha em atenção que neste cenário, pode também especificar o endereço IP da VM.

## <a name="steps-for-manually-configuring-sql-server-connectivity-in-an-azure-vm"></a>Passos para configurar manualmente a conectividade de SQL Server numa VM Azure

Os passos seguintes demonstram como configurar manualmente o conectividade a instância do SQL Server e, em seguida, opcionalmente, ligar-se na Internet com SQL Server Management Studio (SSMS). Tenha em atenção que muitos destes passos são feitos por si quando selecionar as opções de conectividade do SQL Server adequadas no portal.

Antes de poder ligar para a instância do SQL Server a partir de outro VM ou na internet, tem de concluir as seguintes tarefas, tal como descrito nas secções que se seguem:

- [Portas TCP abertas na firewall do Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
- [Configurar o SQL Server para escutar o protocolo TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
- [Configurar o SQL Server para a autenticação de modo misto](#configure-sql-server-for-mixed-mode-authentication)
- [Criar inícios de sessão do SQL Server autenticação](#create-sql-server-authentication-logins)
- [Configurar uma regra de entrada do grupo de segurança de rede](#configure-a-network-security-group-inbound-rule-for-the-vm)
- [Configurar uma etiqueta de DNS para o endereço IP público](#configure-a-dns-label-for-the-public-ip-address)
- [Ligar para o motor de base de dados a partir de outro computador](#connect-to-the-database-engine-from-another-computer)

[AZURE.INCLUDE [Connect to SQL Server in a VM](../../includes/virtual-machines-sql-server-connection-steps.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager-nsg-rule.md)]

[AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Próximos passos

Para ver instruções juntamente com estes passos de conectividade de aprovisionamento, consulte o artigo [aprovisionamento uma máquina de Virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

[Explorar o caminho de formação](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais Azure.

Para obter outros tópicos relacionados com a execução do SQL Server no Azure VMs, consulte o artigo [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
