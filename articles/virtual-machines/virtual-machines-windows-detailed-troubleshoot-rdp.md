<properties
    pageTitle="Detalhadas ambiente de trabalho resolução de problemas remota | Microsoft Azure"
    description="Rever detalhados passos de resolução de problemas de erros de ambiente de trabalho remotos onde não é possível para um máquinas virtuais Windows Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"
    keywords="Não é possível ligar para o ambiente de trabalho remoto, resolver problemas de ambiente de trabalho remoto, é possível ligar o ambiente de trabalho remoto, erros de ambiente de trabalho remotos, resolução de problemas ambiente de trabalho remoto, problemas de ambiente de trabalho remotos
"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Resolução de problemas obter passos detalhados sobre problemas de ligação de ambiente de trabalho remoto para Windows VMs no Azure

Este artigo fornece os passos detalhados de resolução de problemas para diagnosticar e corrigir erros de ambiente de trabalho remoto complexos para baseados no Windows Azure máquinas de virtuais.

> [AZURE.IMPORTANT] Para eliminar os erros mais comuns do ambiente de trabalho remoto, certifique-se ler [o artigo resolução de problemas básico para ambiente de trabalho remoto](virtual-machines-windows-troubleshoot-rdp-connection.md) antes de continuar.

Poderá encontrar uma mensagem de erro de ambiente de trabalho remoto que não se assemelham a qualquer uma das mensagens de erro específicas abrangidas [o](virtual-machines-windows-troubleshoot-rdp-connection.md)guia de resolução de problemas de ambiente de trabalho remoto básica. Siga estes passos para determinar porque é que o cliente de ambiente de trabalho remoto (RDP) não é possível ligar ao serviço de RDP na VM Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se precisar de mais ajuda em qualquer ponto neste artigo, pode contactar os Azure especialistas no [Azure o MSDN e os fóruns de pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Em alternativa, também pode preencher um incidente de suporte Azure. Aceda ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte**. Para obter informações sobre como utilizar o Azure suporte, leia as [FAQ de suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).


## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma ligação de ambiente de trabalho remoto

Os seguintes componentes estão, normalmente uma ligação RDP:

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de continuar, pode ajudá-lo para mentalmente rever o que foi alterado desde a última ligação de ambiente de trabalho remoto efetuada com êxito à VM. Por exemplo:

- O endereço IP público da VM ou do serviço de nuvem que contém a VM (também designada o endereço IP virtual [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) foi alterado. A falha RDP pode dever-a cache de cliente DNS ainda tem o *endereço IP antigo* registado para o nome DNS. A cache DNS cliente e tente ligar a VM novamente. Ou experimente ligar diretamente com o novo VIP.
- Está a utilizar uma aplicação de terceiros para gerir as suas ligações de ambiente de trabalho remoto em vez de utilizar a ligação gerada pelo portal do Azure. Certifique-se de que a configuração da aplicação inclui a porta TCP correta para o tráfego de ambiente de trabalho remoto. Pode verificar esta porta para uma máquina virtual clássica no [portal do Azure](https://portal.azure.com), clicando em definições a VM > pontos finais.


## <a name="preliminary-steps"></a>Passos introdutórios

Antes de prosseguir para o detalhadas resolução de problemas,

- Verifique o estado da máquina virtual no portal do Azure clássico ou no portal do Azure quaisquer problemas óbvios.
- Siga os [passos rápidos fix para erros comuns de RDP no guia de resolução de problemas básico](virtual-machines-windows-troubleshoot-rdp-connection.md#quick-troubleshooting-steps).


Experimente a reestabelecer ligação para a VM através do ambiente de trabalho remoto após estes passos.


## <a name="detailed-troubleshooting-steps"></a>Passos detalhados de resolução de problemas

O cliente de ambiente de trabalho remoto poderá não conseguir atingir o serviço de ambiente de trabalho remoto na VM Azure devido a problemas nas seguintes origens:

- [Computador cliente de ambiente de trabalho remoto](#source-1-remote-desktop-client-computer)
- [Dispositivo de limite de intranet de organização](#source-2-organization-intranet-edge-device)
- [Ponto final de serviço em nuvem e aceder a lista de controlo (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Grupos de segurança de rede](#source-4-network-security-groups)
- [Baseado no Windows Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Origem 1: Computador cliente de ambiente de trabalho remoto

Certifique-se de que o seu computador pode criar ligações de ambiente de trabalho remoto para outra no local, computador baseado no Windows.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_1.png)

Se não conseguir, verifique as seguintes definições no seu computador:

- Uma definição de local firewall que está a bloquear o tráfego de ambiente de trabalho remoto.
- Localmente instalado o software de proxy do cliente que está a impedir que as ligações de ambiente de trabalho remoto.
- Instalado localmente software que está a impedir que as ligações de ambiente de trabalho remoto de monitorização de rede.
- Outros tipos de software de segurança que monitorizar o tráfego ou permitirem/não permitir tipos específicos de tráfego que está a impedir que as ligações de ambiente de trabalho remoto.

Nestes casos, desactivar temporariamente o software e tente ligar-se para um computador local através do ambiente de trabalho remoto. Se pode saber a causa real desta forma, trabalhe com o seu administrador de rede para corrigir as definições de software para permitir ligações de ambiente de trabalho remoto.

## <a name="source-2-organization-intranet-edge-device"></a>Origem 2: Dispositivo de contorno da intranet de organização

Certifique-se de que o computador diretamente ligado à Internet pode criar ligações de ambiente de trabalho remoto para o seu computador virtual Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_2.png)

Se não tiver um computador que está diretamente ligado à Internet, crie e teste a com uma máquina virtual Azure nova num serviço de grupo ou em nuvem de recursos. Para mais informações, consulte o artigo [criar uma máquina virtual a executar o Windows no Azure](virtual-machines-windows-hero-tutorial.md). Pode eliminar a máquina virtual e o grupo de recursos ou serviço na nuvem, após o ensaio.

Se pode criar uma ligação de ambiente de trabalho remoto com um computador diretamente ligado à Internet, verifique o seu dispositivo de limite de intranet de organização para:

- Uma firewall interna bloquear HTTPS ligações à Internet.
- Um servidor proxy prevenir ligações de ambiente de trabalho remoto.
- Deteção de intrusos ou monitorização software a ser executado em dispositivos na sua rede de limite que está a impedir que as ligações de ambiente de trabalho remoto de rede.

Trabalhar com o seu administrador de rede para corrigir as definições do seu dispositivo de limite de intranet de organização para permitir ligações à Internet HTTPS com base no ambiente de trabalho remoto.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem 3: Ponto final de serviço de nuvem e ACL

Para VMs criadas utilizando o modelo de implementação clássico, certifique-se de que outra VM Azure que está na mesma serviço na nuvem ou rede virtual podem ser efetuadas ligações de ambiente de trabalho remoto a sua VM Azure.

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_3.png)

> [AZURE.NOTE] Para máquinas virtuais criadas no Gestor de recursos, avance para o [origem 4: grupos de segurança de rede](#source-4-network-security-groups).

Se não tiver outra máquina virtual no serviço na nuvem mesmo ou rede virtual, crie uma. Siga os passos em [criar uma máquina virtual a executar o Windows no Azure](virtual-machines-windows-hero-tutorial.md). Elimine o teste virtual machine depois do teste.

Se pode ligar através do ambiente de trabalho remoto para uma máquina virtual na rede virtual ou serviço na nuvem da mesma, procurar estas definições:

- A configuração de ponto final para o tráfego de ambiente de trabalho remoto no destino VM: A porta TCP privada do ponto final tem de corresponder à porta TCP no qual está a escutar serviço de ambiente de trabalho remoto a VM (a predefinição é 3389).
- ACL para o ponto final tráfego de ambiente de trabalho remoto no destino VM: ACL permitem-lhe especificar permitidas ou negadas tráfego da Internet com base no respetivo endereço IP de origem. Não foram corretamente configuradas ACL podem impedir que o tráfego de ambiente de trabalho remoto entrada para o ponto final. Selecione o seu ACL para se certificar de que o tráfego recebido a partir do seu endereços IP públicos do seu proxy ou outros servidor edge é permitido. Para obter mais informações, consulte o artigo [o que é uma lista de controlo de acesso de rede (ACL)?](../virtual-network/virtual-networks-acl.md)

Para verificar se o ponto final é a origem do problema, remova o ponto final atual e crie um novo, escolher uma porta aleatória no intervalo 49152 – 65535 para o número de porta externa. Para mais informações, consulte o artigo [como configurar os pontos finais para uma máquina virtual](virtual-machines-windows-classic-setup-endpoints.md).

## <a name="source-4-network-security-groups"></a>Origem de 4: Grupos de segurança de rede

Grupos de segurança de rede permitir que um controlo mais granular de tráfego de entrada e saída permitido. Pode criar regras que abrangem sub-redes e cloud services numa rede virtual Azure. Verificar as suas regras do grupo de segurança de rede para se certificar de que é permitido ambiente de trabalho remoto tráfego da Internet:

- No portal do Azure, selecione a VM
- Clique em **todas as definições de** | **interfaces de rede** e selecione a interface de rede.
- Clique em **todas as definições de** | **grupo de segurança de rede** e selecione o seu grupo de segurança de rede.
- Clique em **todas as definições de** | **regras de entrada de segurança** e certifique-se de que possui uma regra permitindo RDP na porta TCP 3389.
    - Se não tiver uma regra, clique em **Adicionar** para criar uma regra. Introduza **TCP** para o protocolo e, em seguida, **3389** para o intervalo de porta de destino.
    - Certifique-se de que a ação está definida para **Permitir** e clique em OK para guardar a sua nova regra de entrada.


Para obter mais informações, consulte o artigo [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

## <a name="source-5-windows-based-azure-vm"></a>Origem de 5: Baseados no Windows Azure VM

![](./media/virtual-machines-windows-detailed-troubleshoot-rdp/tshootrdp_5.png)

Utilize o [pacote de diagnósticos do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para ver se a falha é devido a máquina virtual Azure própria. Se não conseguir resolver o problema de **conectividade RDP para uma VM Azure (é necessário reiniciar)** este pacote de diagnóstico, siga as instruções [neste](virtual-machines-windows-reset-rdp.md)artigo. Este artigo repõe o serviço de ambiente de trabalho remoto na máquina virtual:

- Ative a regra de predefinido "Ambiente de trabalho remoto" Firewall do Windows (porta TCP 3389).
- Ative as ligações de ambiente de trabalho remoto, definindo o valor de registo HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections como 0.

Tente novamente a ligação do seu computador. Se ainda não conseguir ligar através do ambiente de trabalho remoto, procurar possíveis problemas que se seguem:

- O serviço de ambiente de trabalho remoto não está em execução no destino VM.
- O serviço de ambiente de trabalho remoto não está a escutar porta TCP 3389.
- Firewall do Windows ou qualquer outro local firewall tem uma regra de saída que está a impedir o tráfego de ambiente de trabalho remoto.
- Deteção de intrusos ou monitorização software em execução no Azure máquina virtual da rede está a impedir ligações de ambiente de trabalho remoto.

Para VMs criadas utilizando o modelo clássico de implementação, pode utilizar uma sessão remota do Azure PowerShell à máquina virtual Azure. Em primeiro lugar, tem de instalar um certificado para o serviço de nuvem alojamento a máquina virtual. Aceda a [Configurar seguro PowerShell acesso remoto para máquinas virtuais do Azure](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e transfira o ficheiro de script **InstallWinRMCertAzureVM.ps1** para o computador local.

Em seguida, instale o Azure PowerShell se ainda não o fez. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Em seguida, abra uma linha de comandos do PowerShell do Azure e altere a pasta atual para a localização do ficheiro de script **InstallWinRMCertAzureVM.ps1** . Para executar um script do Azure PowerShell, tem de definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o nível de política atual. Para obter informações sobre como definir o nível adequado, consulte o artigo [ExecutionPolicy conjunto](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha nome da sua subscrição Azure, o nome do serviço na nuvem e o nome de máquina virtual (remover o < e > carateres), e, em seguida, executar estes comandos.

    $subscr="<Name of your Azure subscription>"
    $serviceName="<Name of the cloud service that contains the target virtual machine>"
    $vmName="<Name of the target virtual machine>"
    .\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Pode obter o nome da subscrição correto da propriedade _SubscriptionName_ da apresentação do comando **Get-AzureSubscription** . Pode obter o nome do serviço na nuvem para a máquina virtual da coluna _ServiceName_ na apresentação do comando **Get-AzureVM** .

Verificar se tem o novo certificado. Abra um snap-in certificados para o utilizador actual e procure na pasta **Fidedignos Authorities\Certificates de certificação de raiz** . Deverá visualizar um certificado com o nome DNS do seu serviço de nuvem na coluna emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do Azure PowerShell utilizando estes comandos.

    $uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
    $creds = Get-Credential
    Enter-PSSession -ConnectionUri $uri -Credential $creds

Depois de introduzir credenciais de administrador válido, deverá ver algo semelhante à linha de comandos do Azure PowerShell seguinte:

    [cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

A primeira parte neste pedido é o seu nome de serviço de nuvem que contém o destino VM, que podem ser diferente dos "cloudservice4testing.cloudapp.net". Agora pode emitir Azure PowerShell comandos para este serviço em nuvem investigar problemas mencionados e corrigir a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente os serviços de ambiente de trabalho remoto TCP porta de espera

Se não conseguir executar o [pacote de diagnósticos do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) para o problema de **conectividade RDP para uma VM Azure (é necessário reiniciar)** , no pedido de sessão remoto do Azure PowerShell, execute este comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

A propriedade PortNumber mostra o número da porta atual. Se for necessário, altere o ambiente de trabalho remoto porta número anterior para o valor predefinido (3389) utilizando este comando.

    Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Certifique-se de que a porta ter sido alterada para 3389 utilizando este comando.

    Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Sair da sessão remota do Azure PowerShell utilizando este comando.

    Exit-PSSession

Certifique-se de que o ponto final de ambiente de trabalho remoto para a VM Azure também esteja a utilizar a porta TCP 3398 como o seu porta interna. Reinicie a VM Azure e tente novamente a ligação de ambiente de trabalho remoto.


## <a name="additional-resources"></a>Recursos adicionais

[Pacote de diagnósticos do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Como repor uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas de virtuais do Windows](virtual-machines-windows-reset-rdp.md)

[Como instalar e configurar PowerShell do Azure](../powershell-install-configure.md)

[Resolver problemas de ligações de Shell seguro (SSH) para uma máquina de virtual baseado em Linux Azure](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Resolver problemas de acesso a uma aplicação em execução numa máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)
