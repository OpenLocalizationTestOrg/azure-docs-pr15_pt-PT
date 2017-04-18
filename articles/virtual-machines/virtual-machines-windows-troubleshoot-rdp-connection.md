<properties
    pageTitle="Não é possível RDP para uma VM Azure | Microsoft Azure"
    description="Resolução de problemas quando não conseguir ligar à sua máquina virtual Windows Azure utilizando o ambiente de trabalho remoto"
    keywords="Erro de ambiente de trabalho remoto, o erro de ligação de ambiente de trabalho remoto, é possível ligar ao VM, remoto ambiente de trabalho de resolução de problemas"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/26/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Resolver problemas de ligações de ambiente de trabalho remoto para uma máquina virtual Azure

A ligação de protocolo de ambiente de trabalho remoto (RDP) ao seu baseados no Windows Azure máquina virtual (VM) pode falhar por diversas razões, deixe não é possível aceder ao seu VM. Pode ser o problema com o serviço de ambiente de trabalho remoto na VM, a ligação de rede ou o cliente de ambiente de trabalho remoto no seu computador do anfitrião. Este artigo orienta-o através de alguns dos métodos mais comuns para resolver problemas de ligação RDP. 

Se precisar de mais ajuda em qualquer ponto neste artigo, pode contactar os Azure especialistas no [MSDN Azure e pilha de conteúdo adicional nos fóruns](https://azure.microsoft.com/support/forums/). Em alternativa, pode preencher um incidente de suporte Azure. Ir para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Resolução de problemas de passos rápidos
Após cada etapa de resolução de problemas, experimente voltar a ligar para a VM:

1. Repor a configuração de ambiente de trabalho remoto.
2. Grupo de segurança de rede verificar regras / Cloud Services pontos finais.
3. Reveja VM consola registos.
4. Verificar o estado de funcionamento do recurso VM.
5. Repor a palavra-passe VM.
6. Reinicie o seu VM.
7. Implementar a VM.

Continue a ler se precisar de mais detalhados passos e explicações.

> [AZURE.TIP] Se o botão de **Ligar** para o seu VM estiver a cinzento no portal e não estiver ligado ao Azure através de uma ligação [Encaminhar Express](../expressroute/expressroute-introduction.md) ou [VPN do Site para o Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) , tem de criar e atribuir a VM um endereço IP público antes de poder utilizar RDP. Pode ler mais sobre os [endereços IP públicos no Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Formas de resolução de problemas RDP
Pode resolver problemas de VMs criadas utilizando o modelo de implementação do Gestor de recursos através de um dos seguintes métodos:

- [Azure portal](#using-the-azure-portal) - excelente se precisar de rapidamente repor as credenciais de utilizador ou de configuração de RDP e não tem o Azure tools instalado.
- [Azure PowerShell](#using-azure-powershell) - se estiver familiarizado com uma linha de comandos do PowerShell, repor rapidamente as credenciais de utilizador ou de configuração de RDP utilizando os cmdlets do Azure PowerShell.

Também pode encontrar os passos de resolução de problemas VMs criadas utilizando o [modelo de implementação clássica](#troubleshoot-vms-created-using-the-classic-deployment-model).


<a id="fix-common-remote-desktop-errors"></a>
## <a name="troubleshoot-using-the-azure-portal"></a>Resolução de problemas com o portal do Azure
Após cada etapa de resolução de problemas, experimente ligar à sua VM novamente. Se ainda não consigo ligar, experimente o passo seguinte.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe a configuração de RDP quando ligações remotas são desativadas ou regras da Firewall do Windows estejam a impedir RDP, por exemplo.

    Selecione o seu VM no portal do Azure. Desloque-se para baixo no painel de definições para a secção **suporte + resolução de problemas** junto à parte inferior da lista. Clique no botão **Repor palavra-passe** . Defina o **modo** como **Repor apenas configuração** e, em seguida, clique no botão de **atualização** :

    ![Repor a configuração de RDP no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-rdp.png)

2. **Regras de verificar o grupo de segurança de rede**. Este passo de resolução de problemas verifica se tiver uma regra no seu grupo de segurança de rede para permitir o tráfego RDP. A porta predefinida para RDP é porta TCP 3389. Uma regra para permitir o tráfego RDP poderá não ser criada automaticamente quando criar o seu VM.

    Selecione o seu VM no portal do Azure. Clique nas **interfaces de rede** a partir do painel de definições.

    ![Vista interfaces de rede para uma VM no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-network-interfaces.png)

    Selecione a interface de rede a partir da lista (há normalmente apenas uma):

    ![Selecione a interface de rede no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-interface.png)

    Selecione o **grupo de segurança de rede** para visualizar o grupo de segurança de rede associados a interface de rede:

    ![Selecione grupo de segurança de rede no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/select-nsg.png)

    Certifique-se de que existe uma regra de entrada que permite que o tráfego RDP na porta TCP 3389. O exemplo seguinte mostra uma regra de segurança válida que permita o tráfego RDP. Pode ver `Service` e `Action` estão configurados corretamente:

    ![Verifique se a regra de RDP NSG no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/verify-nsg-rules.png)

    Se não tiver uma regra que permite que o tráfego RDP, [criar uma regra de grupo de segurança de rede](virtual-machines-windows-nsg-quickstart-portal.md). Permitir a porta TCP 3389.

3. **Rever VM diagnósticos de arranque**. Este passo de resolução de problemas revê os registos de consola VM para determinar se a VM é a identificação de um problema. VMs nem todos os têm diagnósticos de arranque ativados, pelo que este passo de resolução de problemas poderá ser opcional.
    
    Passos de resolução de problemas específicos são para além do âmbito deste artigo, mas podem indicar um problema de maior que está a conectividade RDP afectar. Para mais informações sobre como rever os registos de consola e VM captura de ecrã, consulte o artigo [Diagnósticos de arranque para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Verificar o estado de funcionamento do recurso VM**. Verifica se este passo de resolução de problemas, não existem problemas conhecidos com a plataforma Azure que podem ter impacto conectividade para a VM.

    Selecione o seu VM no portal do Azure. Desloque-se para baixo no painel de definições para a secção **suporte + resolução de problemas** junto à parte inferior da lista. Clique no botão de **Estado de funcionamento do recurso** . Uma VM Saudável relatórios como estando **disponíveis**:

    ![Verificar estado de funcionamento do VM recurso no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/check-resource-health.png)

5. **Repor as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe numa conta de administrador local quando estiver certo ou esqueceu-se as credenciais.

    Selecione o seu VM no portal do Azure. Desloque-se para baixo no painel de definições para a secção **suporte + resolução de problemas** junto à parte inferior da lista. Clique no botão **Repor palavra-passe** . Certifique-se de que o **modo** está definido para **Repor a palavra-passe** e, em seguida, introduza o seu nome de utilizador e uma palavra-passe nova. Por fim, clique no botão de **atualização** :

    ![Repor as credenciais de utilizador no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/reset-password.png)

6. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir problemas subjacentes que está a ter dificuldades a VM própria.

    Selecione o seu VM no portal do Azure e clique no separador **Descrição geral** . Clique no botão **reiniciar** :

    ![Reiniciar a VM no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/restart-vm.png)

7. **Implementar a VM**. Este passo de resolução de problemas redeploys a VM a outro anfitrião dentro Azure para corrigir problemas de rede ou a qualquer plataforma subjacente.

    Selecione o seu VM no portal do Azure. Desloque-se para baixo no painel de definições para a secção **suporte + resolução de problemas** junto à parte inferior da lista. Clique no botão **voltar a implementar** e, em seguida, clique em **Implementar novamente**:

    ![Implementar a VM no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/redeploy-vm.png)

    Depois de concluir esta operação, dados do disco efémeras serão perdidos e endereços IP dinâmicos que estão associados a VM são atualizados.

Se ainda encontrar problemas RDP, pode [Abrir um pedido de suporte](https://azure.microsoft.com/support/options/) ou ler os [conceitos de resolução de problemas de RDP mais detalhados e os passos](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-using-azure-powershell"></a>Resolução de problemas com PowerShell do Azure
Se ainda não o fez, [instale e configure o PowerShell Azure mais recente](../powershell-install-configure.md).

Os exemplos seguintes utilizam variáveis, tais como `myResourceGroup`, `myVM`, e `myVMAccessExtension`. Substitua os próprios valores estes nomes de variáveis e localizações.

> [AZURE.NOTE] Repor as credenciais de utilizador e a configuração de RDP utilizando o cmdlet do PowerShell [AzureRmVMAccessExtension conjunto](https://msdn.microsoft.com/library/mt619447.aspx) . Nos exemplos seguintes, `myVMAccessExtension` é o nome que especificar como parte do processo. Se anteriormente trabalhou com a VMAccessAgent, pode obter o nome da extensão do existente utilizando `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` para verificar as propriedades da VM. Para ver o nome, procure na secção 'Extensões' dos resultados.

Após cada etapa de resolução de problemas, experimente ligar à sua VM novamente. Se ainda não consigo ligar, experimente o passo seguinte.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe a configuração de RDP quando ligações remotas são desativadas ou regras da Firewall do Windows estejam a impedir RDP, por exemplo.

    O exemplo a seguir repõe a ligação RDP numa VM denominada `myVM` na `WestUS` localização e, no grupo de recursos com o nome `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```

2. **Regras de verificar o grupo de segurança de rede**. Este passo de resolução de problemas verifica se tiver uma regra no seu grupo de segurança de rede para permitir o tráfego RDP. A porta predefinida para RDP é porta TCP 3389. Uma regra para permitir o tráfego RDP poderá não ser criada automaticamente quando criar o seu VM.

    Em primeiro lugar, atribuir todos os dados de configuração do seu grupo de segurança de rede para o `$rules` variável. O exemplo seguinte obtém informações sobre o grupo de segurança de rede com o nome `myNetworkSecurityGroup` no grupo de recursos com o nome `myResourceGroup`:

    ```powershell
    $rules = Get-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```

    Agora, veja as regras que estão configuradas para este grupo de segurança de rede. Certifique-se de que existe uma regra para permitir a porta TCP 3389 para ligações de entrada da seguinte forma:

    ```powershell
    $rules.SecurityRules
    ```

    O exemplo seguinte mostra uma regra de segurança válida que permita o tráfego RDP. Pode ver `Protocol`, `DestinationPortRange`, `Access`, e `Direction` estão configurados corretamente:

    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```

    Se não tiver uma regra que permite que o tráfego RDP, [criar uma regra de grupo de segurança de rede](virtual-machines-windows-nsg-quickstart-powershell.md). Permitir a porta TCP 3389.

3. **Repor as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe da conta de administrador local que especificou quando a certeza do ou tenha esquecido, as credenciais.

    Em primeiro lugar, especifique o nome de utilizador e uma palavra-passe nova, atribuindo credenciais para a `$cred` variável da seguinte forma:

    ```powershell
    $cred=Get-Credential
    ```

    Agora, Atualize as credenciais no seu VM. O exemplo seguinte atualiza as credenciais de uma VM denominada `myVM` na `WestUS` localização e, no grupo de recursos com o nome `myResourceGroup`:

    ```powershell
    Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```

4. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir problemas subjacentes que está a ter dificuldades a VM própria.

    O exemplo seguinte é reiniciado VM denominada `myVM` no grupo de recursos com o nome `myResourceGroup`:

    ```powershell
    Restart-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```

5. **Implementar a VM**. Este passo de resolução de problemas redeploys a VM a outro anfitrião dentro Azure para corrigir problemas de rede ou a qualquer plataforma subjacente.

    O exemplo seguinte redeploys VM denominada `myVM` na `WestUS` localização e, no grupo de recursos com o nome `myResourceGroup`:

    ```powershell
    Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

Se ainda encontrar problemas RDP, pode [Abrir um pedido de suporte](https://azure.microsoft.com/support/options/) ou ler os [conceitos de resolução de problemas de RDP mais detalhados e os passos](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Resolver problemas de VMs criadas utilizando o modelo de implementação clássico

Após cada etapa de resolução de problemas, experimente voltar a ligar para a VM.

1. **Repor a sua ligação de RDP**. Este passo de resolução de problemas repõe a configuração de RDP quando ligações remotas são desativadas ou regras da Firewall do Windows estejam a impedir RDP, por exemplo.

    Selecione o seu VM no portal do Azure. Clique na **... Mais** botão, em seguida, clique em **Repor acesso remoto**:

    ![Repor a configuração de RDP no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-rdp.png)

2.  **Os pontos finais de verificar serviços em nuvem**. Este passo de resolução de problemas verifica que tem os pontos finais na sua serviços em nuvem para permitir o tráfego RDP. A porta predefinida para RDP é porta TCP 3389. Uma regra para permitir o tráfego RDP poderá não ser criada automaticamente quando criar o seu VM.

    Selecione o seu VM no portal do Azure. Clique no botão **pontos finais** para ver os pontos finais atualmente configurados para o seu VM. Certifique-se de que os pontos finais existem de que permitir o tráfego RDP na porta TCP 3389.
    
    O exemplo seguinte mostra os pontos finais válidos que permita o tráfego RDP:

    ![Verifique se os pontos finais de serviços em nuvem no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)

    Se não tiver um ponto final que permite que o tráfego RDP, [criar um ponto final de serviços em nuvem](virtual-machines-windows-classic-setup-endpoints.md). Permita que TCP porta privada 3389.

3. **Rever VM diagnósticos de arranque**. Este passo de resolução de problemas revê os registos de consola VM para determinar se a VM é a identificação de um problema. VMs nem todos os têm diagnósticos de arranque ativados, pelo que este passo de resolução de problemas poderá ser opcional.
    
    Passos de resolução de problemas específicos são para além do âmbito deste artigo, mas podem indicar um problema de maior que está a conectividade RDP afectar. Para mais informações sobre como rever os registos de consola e VM captura de ecrã, consulte o artigo [Diagnósticos de arranque para VMs](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

4. **Verificar o estado de funcionamento do recurso VM**. Verifica se este passo de resolução de problemas, não existem problemas conhecidos com a plataforma Azure que podem ter impacto conectividade para a VM.

    Selecione o seu VM no portal do Azure. Desloque-se para baixo no painel de definições para a secção **suporte + resolução de problemas** junto à parte inferior da lista. Clique no botão de **Estado de funcionamento do recurso** . Uma VM Saudável relatórios como estando **disponíveis**:

    ![Verificar estado de funcionamento do VM recurso no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-check-resource-health.png)

5. **Repor as credenciais de utilizador**. Este passo de resolução de problemas repõe a palavra-passe da conta de administrador local que especificou quando são certeza ou esqueceu-se as credenciais.

    Selecione o seu VM no portal do Azure. Desloque-se para baixo no painel de definições para a secção **suporte + resolução de problemas** junto à parte inferior da lista. Clique no botão **Repor palavra-passe** . Introduza o seu nome de utilizador e uma palavra-passe nova. Por fim, clique no botão **Guardar** :

    ![Repor as credenciais de utilizador no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-reset-password.png)

6. **Reinicie o seu VM**. Este passo de resolução de problemas pode corrigir problemas subjacentes que está a ter dificuldades a VM própria.

    Selecione o seu VM no portal do Azure e clique no separador **Descrição geral** . Clique no botão **reiniciar** :

    ![Reiniciar a VM no portal do Azure](./media/virtual-machines-windows-troubleshoot-rdp-connection/classic-restart-vm.png)
    
Se ainda encontrar problemas RDP, pode [Abrir um pedido de suporte](https://azure.microsoft.com/support/options/) ou ler os [conceitos de resolução de problemas de RDP mais detalhados e os passos](virtual-machines-windows-detailed-troubleshoot-rdp.md).


## <a name="troubleshoot-specific-rdp-errors"></a>Resolver problemas de erros RDP específicos
Poderá encontrar uma mensagem de erro específico quando está a tentar ligar a VM através do RDP. Seguem-se as mensagens de erro mais comuns:

- [A sessão remota foi desligada porque não existem não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdplicense).
- [Ambiente de trabalho remoto não é possível localizar o computador "nome"](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpname).
- Ocorreu erro [uma autenticação. Autoridade de segurança Local não pode ser contactada](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpauth).
- [Erro de segurança do Windows: as suas credenciais não funcionarem](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#wincred).
- [Neste computador não é possível ligar para o computador remoto](virtual-machines-windows-troubleshoot-specific-rdp-errors.md#rdpconnect).


## <a name="additional-resources"></a>Recursos adicionais
Se nenhuma destes erros ocorreu e ainda não consigo ligar para a VM através do ambiente de trabalho remoto, leia detalhados de [resolução de problemas de guia para ambiente de trabalho remoto](virtual-machines-windows-detailed-troubleshoot-rdp.md).

- [Pacote de diagnósticos do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Para aceder a aplicações em execução numa VM passos de resolução de problemas, consulte o artigo [resolução de problemas acesso à aplicação em execução numa VM Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Se estiver a ter problemas a utilização da Shell de seguro (SSH) para ligar a uma VM Linux no Azure, consulte o artigo [resolver problemas de SSH ligações para uma VM Linux no Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).
