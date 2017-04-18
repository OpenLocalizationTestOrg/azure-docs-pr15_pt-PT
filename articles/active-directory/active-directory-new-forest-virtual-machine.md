<properties
    pageTitle="Instalar uma floresta do Active Directory numa rede virtual Azure | Microsoft Azure"
    description="Tutorial que explica como criar uma nova floresta do Active Directory numa máquina virtual (VM) numa rede Virtual Azure."
    services="active-directory, virtual-network"
    keywords="máquina de virtual do Active directory, floresta do active directory de instalação, vídeos azure do active directory "
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    tags=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Instalar uma nova floresta do Active Directory numa rede virtual Azure

Este tópico mostra como criar um novo ambiente do Active Directory do Windows Server numa rede virtual Azure numa máquina virtual (VM) numa [rede virtual Azure](../virtual-network/virtual-networks-overview.md). Neste caso, a rede virtual Azure não está ligada a uma rede no local.

Também poderá estar interessado nestes tópicos relacionados:

- Para um vídeo que mostra estes passos, consulte o artigo [sobre como instalar uma nova floresta do Active Directory numa rede virtual Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
- Opcionalmente, pode [Configurar uma VPN do site para o site](../vpn-gateway/vpn-gateway-site-to-site-create.md) e, em seguida, quer instalar uma nova floresta ou expandir uma floresta no local para uma rede virtual Azure. Para esses passos, consulte o artigo [instalar uma réplica Active Directory controlador de domínio numa rede Virtual Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md).
-  Para obter orientações conceptual sobre como instalar o Active Directory Domain Services (AD DS) numa rede virtual Azure, consulte [diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>Diagrama de cenário

Neste cenário, os utilizadores externos tem de aceder a aplicações que são executados em servidores de domínio. As VMs que executam os servidores de aplicações e os que são executados controladores de domínio VMs são instalados instalado no seu próprio serviço em nuvem numa rede virtual Azure. Também são incluídos dentro de uma disponibilidade configurar para tolerância a falhas melhorado.

![Ativa floresta directório numa máquinas virtuais na rede Virtual Azure ][1] 7
## <a name="how-does-this-differ-from-on-premises"></a>Como é que isto diferem no local?

Não é muito diferença entre instalar um controlador de domínio no Azure versus no local. As principais diferenças estão listadas na tabela seguinte.

Para configurar...  | No local  | Azure rede virtual
------------- | -------------  | ------------
**Endereço IP do controlador de domínio**  | Atribuir endereço IP estático nas propriedades de adaptador de rede   | Execute o cmdlet Set-AzureStaticVNetIP para atribuir um endereço IP estático
**Resolução de cliente DNS**  | Definir preferido e DNS alternativo endereço do servidor da rede propriedades da placa de membros do domínio   | Configurar o endereço do servidor DNS no as propriedades de rede virtual
**Armazenamento de base de dados do Active Directory**  | Opcionalmente, alterar a localização de armazenamento predefinida de C:\  | Precisar de alterar a localização de armazenamento predefinida de C:\



## <a name="create-an-azure-virtual-network"></a>Criar uma rede virtual Azure

1. Inicie sessão no portal do Azure clássico.
2. Crie uma rede virtual. Clique em **redes** > **criar uma rede virtual**. Utilize os valores na tabela seguinte para concluir o assistente.

    Na página do assistente...  | Especificar estes valores
    ------------- | -------------
    **Detalhes de rede virtual**  | <p>Nome: Introduza um nome para a sua rede virtual</p><p>Região: Selecione a região mais próxima</p>
    **VPN e DNS**  | <p>Deixe o servidor DNS em branco</p><p>Não selecione qualquer uma das opções de VPN</p>
    **Espaços de endereços de rede virtual**  | <p>Nome de sub-rede: introduza um nome para a sua sub-rede aceda</p><p>IP inicial: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>Criar VMs para executar o controlador de domínio e funções de servidor DNS

Repita os passos seguintes para criar VMs para alojar a função CC, conforme necessário. Deve implementar CDs virtuais, pelo menos, duas para fornecer tolerância a falhas e redundância. Se a rede virtual Azure inclui CDs, pelo menos, duas que estão configurados da mesma forma (isto é, são ambos os GC, executar DNS server, e nenhuma detém qualquer função FSMO e assim sucessivamente), em seguida, coloque os VMs que são aqueles executados CDs uma disponibilidade definido para tolerância a falhas melhorado.

Para criar os VMs utilizando o Windows PowerShell em vez de IU, consulte o artigo [Utilizar o PowerShell Azure para criar e pré-configurar baseados no Windows máquinas virtuais](../virtual-machines/virtual-machines-windows-classic-create-powershell.md).

1. No portal do clássico, clique em **Novo** > **Calcular** > **Máquina Virtual** > **a partir da Galeria**. Utilize os seguintes valores para concluir o assistente. Aceite o valor predefinido para uma definição, a menos que outro valor é sugerido ou necessário.

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

## <a name="install-windows-server-active-directory"></a>Instalar o Windows Server Active Directory

Utilizar a mesma rotina para [instalar o AD DS](https://technet.microsoft.com/library/jj574166.aspx) que utiliza no local (ou seja, pode utilizar a IU, um ficheiro de resposta ou o Windows PowerShell). Tem de fornecer as credenciais de administrador para instalar uma nova floresta. Para especificar a localização para a base de dados do Active Directory, registos de início e SYSVOL, altere a localização de armazenamento predefinida de unidade de sistema operativo para o disco de dados adicionais que anexou para a VM.

Depois de concluir a instalação do Centro de dados, ligar para a VM novamente e inicie sessão para o Centro de dados. Lembre-se especificar as credenciais de domínio.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Repor o servidor de DNS para a rede Azure virtual

1. Repor a definição de reencaminhador DNS no novo servidor CC/DNS.
  1. No Gestor de servidor, clique em **Ferramentas** > **DNS**.
  2. No **Gestor de DNS**, com o botão direito no nome do servidor de DNS e clique em **Propriedades**.
  3. No separador **reencaminhadores** , clique em do endereço IP do reencaminhador e clique em **Editar**.  Selecione o endereço IP e clique em **Eliminar**.
  4. Clique em **OK** para fechar o editor e **Ok** novamente para fechar as propriedades do servidor DNS.
2. Atualize a definição do servidor DNS para a rede virtual.
  1. Clique em **Redes virtuais** > faça duplo clique em rede virtual criou > **Configurar** > **servidores DNS**, escreva o nome e DIP de um das VMs que é executada a função de servidor do Centro de dados/DNS e clique em **Guardar**.
  2. Selecione a VM e clique em **reiniciar** acionado VM para configurar definições de resolução DNS com o endereço IP do novo servidor de DNS.


## <a name="create-vms-for-domain-members"></a>Criar VMs para membros de domínio

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


## <a name="see-also"></a>Consulte também

-  [Como instalar uma nova floresta do Active Directory numa rede virtual Azure](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
-  [Diretrizes para implementar o Active Directory do Windows Server em máquinas virtuais Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [Configurar uma VPN do Site para o Site](../vpn-gateway/vpn-gateway-site-to-site-create.md)
-  [Instalar uma réplica do Active Directory Domain controlador numa rede virtual Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
-  [Microsoft Azure IT Pro IaaS: os conceitos básicos da Máquina Virtual (01)](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
-  [Microsoft Azure IT Pro IaaS: (05) criar redes virtuais e a conectividade de publicação em local](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
-  [Descrição geral de rede virtual](../virtual-network/virtual-networks-overview.md)
-  [Como instalar e configurar PowerShell do Azure](../powershell-install-configure.md)
-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
-  [Referência do Cmdlet Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)
-  [Definir o endereço IP estático Azure VM](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
-  [Como atribuir IP estático VM Azure](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
-  [Instalar uma nova floresta do Active Directory](https://technet.microsoft.com/library/jj574166.aspx)
-  [Introdução do Active Directory Domain Services (AD DS) virtualização (nível 100)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
