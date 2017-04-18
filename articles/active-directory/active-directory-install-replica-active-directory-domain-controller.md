<properties
    pageTitle="Instalar uma réplica do controlador de domínio do Active Directory no Azure | Microsoft Azure"
    description="Tutorial que explica como instalar um controlador de domínio a partir de uma floresta do Active Directory no local numa máquina virtual Azure."
    services="virtual-network"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="curtand"/>


# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Instalar uma réplica do controlador de domínio do Active Directory numa rede virtual Azure

Este tópico mostra como instalar controladores de domínio adicionais (também conhecido como réplica CDs) para um domínio do Active Directory no local no Azure máquinas virtuais (VMs) numa rede virtual Azure.

Também poderá estar interessado nestes tópicos relacionados:

-  Opcionalmente, pode instalar uma nova floresta do Active Directory numa rede Azure virtual. Para esses passos, consulte o artigo [instalar uma nova floresta do Active Directory numa rede virtual Azure](../active-directory/active-directory-new-forest-virtual-machine.md).
-  Para obter orientações conceptual sobre como instalar o Active Directory Domain Services (AD DS) numa rede virtual Azure, consulte [diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).


## <a name="scenario-diagram"></a>Diagrama de cenário

Neste cenário, os utilizadores externos tem de aceder a aplicações que são executados em servidores de domínio. Os VMs que execute os servidores de aplicações e os CDs réplica estão instaladas numa rede virtual Azure. A rede virtual pode ser ligada à rede no local através de uma ligação [VPN do site para o site](../vpn-gateway/vpn-gateway-site-to-site-create.md) , conforme mostrado no seguinte diagrama ou pode utilizar [ExpressRoute](../expressroute/expressroute-locations-providers.md) para uma ligação mais rápida.

Os servidores de aplicações e os CDs são implementados dentro de serviços em nuvem separada para distribuir o processamento de cluster e dentro de [conjuntos de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md) para tolerância a falhas melhorado.
Os CDs replicam entre si e com CDs no local através da utilização de replicação do Active Directory. Não existem ferramentas de sincronização são necessárias.

![Diagrama PC réplica do controlador de domínio do Active Directory uma vnet Azure][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Criar um site do Active Directory para a rede virtual Azure

É uma boa ideia para criar um site no Active Directory que representa a região de rede correspondente à rede virtual. Que ajuda a otimizar a autenticação, replicação e outras operações de localização do Centro de dados. Os passos seguintes explicam como criar um site e mais fundo, consulte o artigo [Adicionar um novo Site](https://technet.microsoft.com/library/cc781496.aspx).

1. Abra Sites do Active Directory e serviços: **Gestor de servidor** > **Ferramentas** > **do Active Directory Sites e serviços**.
2. Criar um site para representar a região onde criou uma rede virtual Azure: clique em **Sites** > **ação** > **novo site** > escreva o nome do novo site, como o Azure-nos oeste > Selecione uma ligação de site > **OK**.
3. Criar uma sub-rede e associar o novo site: faça duplo clique em **Sites** > botão direito do rato **sub-redes** > **nova sub-rede** > introduza o intervalo de endereços IP da rede virtual (como 10.1.0.0/16 no diagrama cenário) > selecione o novo site Azure > **OK**.

## <a name="create-an-azure-virtual-network"></a>Criar uma rede virtual Azure

1. No [portal clássica Azure](https://manage.windowsazure.com), clique em **Novo** > **Serviços de rede** > **Rede Virtual** > **Personalizada criar** e utilizar os seguintes valores para concluir o assistente.

    Na página do assistente...  | Especificar estes valores
    ------------- | -------------
    **Detalhes de rede virtual**  | <p>Nome: Escreva um nome para a rede virtual, tal como WestUSVNet.</p><p>Região: Escolha a região mais próximo.</p>
    **Conectividade VPN e DNS**  | <p>Servidores DNS: Especifique o nome e endereço IP de um ou mais servidores DNS no local.</p><p>Conectividade: Selecione **Configurar uma VPN do site para o site**.</p><p>Rede local: Especifique uma nova rede local.</p><p>Se estiver a utilizar ExpressRoute em vez de uma VPN, consulte o artigo [Configurar uma ligação de ExpressRoute através de um fornecedor do Exchange](../expressroute/expressroute-locations-providers.md).</p>
    **Conectividade de site para o site**  | <p>Nome: Escreva um nome para a rede no local.</p><p>Endereço IP do dispositivo de VPN: Especifique o endereço IP público do dispositivo que irá ligar à rede virtual. Não é possível localizar o dispositivo VPN atrás de um NAT.</p><p>Endereço: Especifique os intervalos de endereços para a sua rede no local (como 192.168.0.0/16 no diagrama cenário).</p>
    **Espaços de endereços de rede virtual**  | <p>Espaço de endereços: Especifique o intervalo de endereços IP para VMs que pretende executar na rede Azure virtual (como 10.1.0.0/16 no diagrama cenário). Não é possível sobrepor este intervalo de endereços com os intervalos de endereços da rede no local.</p><p>Sub-redes: Especifique um nome e endereço para uma sub-rede para os servidores de aplicação (tal como front-end, 10.1.1.0/24) e para os CDs (tal como back-end, 10.1.2.0/24).</p><p>Clique em **Adicionar sub-rede de gateway**.</p>

2. Em seguida, irá configurar o gateway de rede virtual para criar uma ligação de VPN segura do site para o site. Consulte [configurar um Gateway Virtual de rede](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obter as instruções.
3. Crie a ligação de VPN do site para o site entre a nova rede virtual e um dispositivo de VPN no local. Consulte [configurar um Gateway Virtual de rede](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) para obter as instruções.


## <a name="create-azure-vms-for-the-dc-roles"></a>Criar Azure VMs para as funções do Centro de dados

Repita os passos seguintes para criar VMs para alojar a função CC, conforme necessário. Deve implementar CDs virtuais, pelo menos, duas para fornecer tolerância a falhas e redundância. Se a rede virtual Azure inclui CDs, pelo menos, duas que estão configurados da mesma forma (isto é, são ambos os GC, executar DNS server, e nenhuma detém qualquer função FSMO e assim sucessivamente), em seguida, coloque os VMs que são aqueles executados CDs uma disponibilidade definido para tolerância a falhas melhorado.
Para criar os VMs utilizando o Windows PowerShell em vez de IU, consulte o artigo [Utilizar o PowerShell Azure para criar e pré-configurar baseados no Windows máquinas virtuais](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. No [portal clássica Azure](https://manage.windowsazure.com), clique em **Novo** > **Calcular** > **Máquina Virtual** > **a partir da Galeria**. Utilize os seguintes valores para concluir o assistente. Aceite o valor predefinido para uma definição, a menos que outro valor é sugerido ou necessário.

    Na página do assistente...  | Especificar estes valores
    ------------- | -------------
    **Escolher uma imagem**  | Centro de dados do Windows Server 2012 R2
    **Configuração de máquina virtual**  | <p>Nome de máquina virtual: Escreva um nome de uma etiqueta (tal como AzureDC1).</p><p>Novo nome de utilizador: Escreva o nome de um utilizador. Este utilizador será um membro do grupo Administradores na VM local. Terá este nome iniciar sessão para a VM pela primeira vez. A conta incorporada denominada administrador não irá funcionar.</p><p>Nova palavra-passe/confirmar: Escreva uma palavra-passe</p>
    **Configuração de máquina virtual**  | <p>Serviço em nuvem: Selecione <b>criar um novo serviço de nuvem</b> para a primeira VM e selecione esse nome de serviço na nuvem mesmo quando cria VMs mais que vão alojar a função do Centro de dados.</p><p>Nome de DNS do serviço de nuvem: Especifique um nome exclusivo globalmente</p><p>Região/grupo afinidade/rede Virtual: Especifique o nome de rede virtual (como WestUSVNet).</p><p>Conta de armazenamento: Selecione <b>utilizar uma conta de armazenamento gerados automaticamente</b> para a primeira VM e, em seguida, selecione esse mesmo nome de conta de armazenamento quando cria VMs mais que vão alojar a função do Centro de dados.</p><p>Conjunto de disponibilidade: Selecione <b>criar um conjunto de disponibilidade</b>.</p><p>Nome do conjunto de disponibilidade: escreva um nome para a disponibilidade de conjunto de quando criar a primeira VM e, em seguida, selecione que o mesmo nome quando cria VMs mais.</p>
    **Configuração de máquina virtual**  | <p>Selecione <b>instalar o agente VM</b> e extensões de que precisar.</p>
2. Anexe um disco a cada VM que será executada a função de servidor do Centro de dados. O disco adicional é necessário para armazenar a base de dados, registos de início e SYSVOL AD. Especifique um tamanho para o disco (por exemplo, 10 GB) e deixe a **Preferência de Cache de anfitrião** configurada como **nenhuma**. Para obter os passos, consulte o artigo [como anexar um disco de dados para uma máquina de Virtual do Windows](../virtual-machines/virtual-machines-windows-classic-attach-disk.md).
3. Após pela primeira vez iniciar sessão para a VM, abra o **Gestor de servidor** > **Serviços de armazenamento de ficheiros e** para criar um volume neste disco utilizando o NTFS.
4. Reserve um endereço IP estático para VMs irão executar a função do Centro de dados. Para reservar um endereço IP estático, transfira o Microsoft Web plataforma Installer e, em seguida, [instale o PowerShell do Azure](../powershell-install-configure.md) e execute o cmdlet Set-AzureStaticVNetIP. Por exemplo:

    ' Get AzureVM - ServiceName AzureDC1-nome AzureDC1 | Definir AzureStaticVNetIP - endereço IP 10.0.0.4 | AzureVM de actualização

Para mais informações sobre como definir um endereço IP estático, consulte o artigo [configurar um endereço IP estático interna para uma VM](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Instalar o AD DS Azure VMs

Inicie sessão numa VM e certifique-se de que tenha conectividade através da ligação de VPN ou ExpressRoute de site para o site para recursos na sua rede no local. Em seguida, instale o AD DS no VMs Azure. Pode utilizar o mesmo processo que utilizar para instalar um centro de dados adicional na sua rede no local (UI, Windows PowerShell ou um ficheiro de resposta). Como instalar o AD DS, certifique-se de que especificar o novo volume para a localização da base de dados AD, registos de início e SYSVOL. Se precisar de um atualizador na instalação do AD DS, consulte o artigo [Instalar o Active Directory Domain Services (nível 100)](https://technet.microsoft.com/library/hh472162.aspx) ou [instalar um controlador de domínio do réplica Windows Server 2012 num domínio existente (Nível 200)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>Reconfigurar o servidor de DNS para a rede virtual

1. No [portal clássica Azure](https://manage.windowsazure.com), clique no nome da rede virtual e, em seguida, clique no separador **Configurar** [reconfigurar os endereços IP do servidor DNS para a sua rede virtual](../virtual-network/virtual-networks-manage-dns-in-vnet.md) para utilizar os endereços IP estáticos atribuídos à réplica CDs em vez dos endereços IP dos servidores DNS no local.

2. Para se certificar de que todos os réplica CC VMs da rede virtual estão configurados com para utilizar os servidores DNS da rede virtual, clique em **máquinas virtuais**, clique na coluna Estado para cada VM e, em seguida, clique em **reiniciar**. Aguarde até que a VM mostra **a executar o** estado antes de tentar iniciar sessão para o mesmo.

## <a name="create-vms-for-application-servers"></a>Criar VMs para os servidores de aplicações

1. Repita os passos seguintes para criar VMs para executar como os servidores de aplicações. Aceite o valor predefinido para uma definição, a menos que outro valor é sugerido ou necessário.

    Na página do assistente...  | Especificar estes valores
    ------------- | -------------
    **Escolher uma imagem**  | Centro de dados do Windows Server 2012 R2
    **Configuração de máquina virtual**  | <p>Nome de máquina virtual: Escreva um nome de uma etiqueta (tal como AppServer1).</p><p>Novo nome de utilizador: Escreva o nome de um utilizador. Este utilizador será um membro do grupo Administradores na VM local. Terá este nome iniciar sessão para a VM pela primeira vez. A conta incorporada denominada administrador não irá funcionar.</p><p>Nova palavra-passe/confirmar: Escreva uma palavra-passe</p>
    **Configuração de máquina virtual**  | <p>Serviço em nuvem: Selecione **criar um novo serviço de nuvem** para a primeira VM e selecione esse nome de serviço na nuvem mesmo quando cria VMs mais que vão alojar a aplicação.</p><p>Nome de DNS do serviço de nuvem: Especifique um nome exclusivo globalmente</p><p>Região/grupo afinidade/rede Virtual: Especifique o nome de rede virtual (como WestUSVNet).</p><p>Conta de armazenamento: Selecione **utilizar uma conta de armazenamento gerados automaticamente** para a primeira VM e, em seguida, selecione esse mesmo nome de conta de armazenamento quando cria VMs mais que vão alojar a aplicação.</p><p>Conjunto de disponibilidade: Selecione **criar um conjunto de disponibilidade**.</p><p>Nome do conjunto de disponibilidade: escreva um nome para a disponibilidade de conjunto de quando criar a primeira VM e, em seguida, selecione que o mesmo nome quando cria VMs mais.</p>
    **Configuração de máquina virtual**  | <p>Selecione <b>instalar o agente VM</b> e extensões de que precisar.</p>

2. Depois de cada VM está aprovisionado, inicie sessão e associá-lo para o domínio. No **Gestor de servidor**, clique em **Local Server** > **grupo de trabalho** > **alteração...** e, em seguida, selecione o **domínio** e escreva o nome do seu domínio no local. Fornecer as credenciais de um utilizador de domínio e, em seguida, reinicie a VM para concluir a associação de domínio.

Para criar os VMs utilizando o Windows PowerShell em vez de IU, consulte o artigo [Utilizar o PowerShell Azure para criar e pré-configurar baseados no Windows máquinas virtuais](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

Para mais informações sobre como utilizar o Windows PowerShell, consulte o artigo [Introdução ao Azure Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx) e de [Referência do Cmdlet Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx).

## <a name="additional-resources"></a>Recursos adicionais

-  [Diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
-  [Como carregar controladores de domínio de Hyper-V do existentes no local para Azure utilizando o PowerShell do Azure](http://support.microsoft.com/kb/2904015)
-  [Instalar uma nova floresta do Active Directory numa rede virtual Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
-  [Azure rede Virtual](../virtual-network/virtual-networks-overview.md)
-  [Microsoft Azure IT Pro IaaS: os conceitos básicos da Máquina Virtual (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e a conectividade de publicação em local](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Cmdlets de gestão do Azure](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
