<properties 
   pageTitle="Resolver problemas de grupos de segurança de rede - Portal | Microsoft Azure"
   description="Saiba como resolver problemas de rede grupos de segurança no modelo de implementação de Gestor de recursos do Azure utilizando o Portal do Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="AnithaAdusumilli"
   manager="narayan"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="anithaa" />

# <a name="troubleshoot-network-security-groups-using-the-azure-portal"></a>Resolver problemas de grupos de segurança de rede utilizando o Portal do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](virtual-network-nsg-troubleshoot-portal.md)
- [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)

Se configurou grupos de segurança de rede (NSGs) no seu máquina virtual (VM) e estiver a ter problemas de conectividade VM, este artigo fornece uma descrição geral das capacidades de diagnóstico para NSGs ajudar a resolver ainda mais.

NSGs permitem-lhe para controlar os tipos de tráfego esse fluxo e terminar a sua máquinas virtuais (VMs). NSGs podem ser aplicadas a sub-redes numa rede Virtual Azure (VNet), interfaces de rede (NIC) ou ambas. As regras eficazes aplicadas a uma NIC são uma agregação das regras que existam no NSGs aplicados a uma imagem e de sub-rede que está ligado ao. Regras através destes NSGs, por vezes, podem entrar em conflito com os outros e afetar a conectividade da rede uma VM.  

Pode ver todas as regras de segurança eficaz a partir do seu NSGs, tal como aplicada no NIC seu VM. Este artigo mostra como resolver problemas de conectividade VM utilizar estas regras no modelo de implementação de Gestor de recursos do Azure. Se não estiver familiarizado com os conceitos VNet e NSG, leia os artigos descrição geral [da rede Virtual](virtual-networks-overview.md) e [grupos de segurança de rede](virtual-networks-nsg.md) .

## <a name="using-effective-security-rules-to-troubleshoot-vm-traffic-flow"></a>Utilizar regras de segurança eficaz para resolver problemas de fluxo de tráfego VM

O cenário que se segue é um exemplo de um problema de ligação comuns:

Uma VM denominada *VM1* faz parte de uma sub-rede denominada *Sub-rede1* dentro de uma VNet *WestUS VNet1*com o nome. Uma tentativa de ligação para a VM utilizando o RDP através da porta TCP 3389 falha. NSGs são aplicados à NIC *VM1 NIC1* e de sub-rede *Sub-rede1*. O tráfego para a porta TCP 3389 é permitido no NSG associada a interface de rede *VM1 NIC1*, no entanto, TCP ping para falha de porta 3389 do VM1.

Enquanto este exemplo utiliza a porta TCP 3389, os passos seguintes podem ser utilizados para determinar falhas de ligação de entrada e saída sobre qualquer porta.

### <a name="view-effective-security-rules-for-a-virtual-machine"></a>Regras de segurança eficaz de vista para uma máquina virtual

Conclua os passos seguintes para resolver problemas de NSGs para uma VM:

Pode ver uma lista completa das regras de segurança eficaz numa NIC, a partir da VM própria. Também pode adicionar, modificar e eliminar regras NSG NIC e sub-rede de pá regras eficaz, se tem permissões para efetuar estas operações.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**e, em seguida, clique em **máquinas virtuais** na lista que é apresentada.
3. Selecione uma VM para resolver a partir da lista que aparece e é apresentada uma pá VM com opções.
4. Clique em **diagnosticar & resolver problemas de** e, em seguida, selecione um problema comum. Neste exemplo, **não é possível ligo à minha VM Windows** está selecionada. 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image1.png)

5. Passos aparecem no problema, conforme apresentado na seguinte imagem: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image2.png)

    Clique em *regras de grupo de segurança eficaz* na lista de passos recomendados.

6. Aparece a pá **obter as regras de segurança eficaz** , conforme apresentado na seguinte imagem:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image3.png)

    Tenha em atenção as seguintes secções da imagem:

    - **Âmbito:** A VM definida para *VM1*, selecionada no passo 3.
    - **Interface de rede:** *VM1 NIC1* está selecionada. Uma VM pode ter várias interfaces de rede (NIC). Cada NIC pode ter regras de segurança eficaz exclusivo. Quando resolução de problemas, poderá ser necessário visualizar as regras de segurança eficaz para cada NIC.
    - **Associadas NSGs:** NSGs podem ser aplicadas a NIC e sub-rede que NIC está ligado. Na imagem, uma NSG foi aplicado para NIC e sub-rede que está ligado ao. Pode clicar nos nomes NSG diretamente modificar regras nas NSGs.
    - **VM1 nsg separador:** A lista de regras apresentadas na imagem está para NSG aplicado à NIC. Várias regras predefinidas são criadas por Azure sempre que é criada uma NSG. Não é possível remover as regras de predefinidas, mas pode substituí-las com regras de prioridade alta. Para obter mais informações sobre regras de predefinido, leia o artigo [Descrição geral NSG](virtual-networks-nsg.md#default-rules) .
    - **Coluna de destino:** Algumas das regras têm texto na coluna, enquanto que as outras pessoas tiverem prefixos de endereço. O texto é o nome de etiquetas de predefinido aplicadas para a regra de segurança quando foi criada. As etiquetas são fornecidos pelo sistema de identificadores que representam vários prefixos. Selecionar uma regra com uma etiqueta, tal como *AllowInternetOutBound*, lista os prefixos a pá **prefixos de endereço** .
    - **Transferir:** A lista de regras pode ser longa. Pode transferir um ficheiro. csv das regras para uma análise offline ao clicar em **Transferir** e guardar o ficheiro.
    - **AllowRDP** Regra de entrada: esta regra permite ligações RDP para a VM.
7. Clique no separador **Sub-rede1 NSG** para visualizar as regras eficazes de NSG aplicado à sub-rede, conforme apresentado na seguinte imagem: 

    ![](./media/virtual-network-nsg-troubleshoot-portal/image4.png)

    Repare que a regra de **entrada** *denyRDP* . Regras de entrada aplicadas ao sub-rede são avaliadas antes de regras aplicadas na interface da rede. Uma vez que a regra negar é aplicada a sub-rede, o pedido para ligar à TCP 3389 falhar, uma vez que a regra de permitir na NIC nunca é avaliada. 

    A regra *denyRDP* é o motivo por que motivo está a falhar a ligação RDP. Remoção do mesmo deverá resolver o problema.

    >[AZURE.NOTE]Se a VM associada a NIC não está num Estado de execução ou NSGs ainda não foram aplicados à imagem ou sub-rede, sem as regras são apresentadas.

8. Para editar regras NSG, clique em *Sub-rede1 NSG* na secção **NSGs associado** .
   Esta ação abre a pá **Sub-rede1 NSG** . Pode editar diretamente as regras clicando em **regras de entrada de segurança**.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image7.png)

9. Depois de remover a regra de entrada *denyRDP* na **Sub-rede1 NSG** e adicionar uma regra de *allowRDP* , a lista de regras eficaz aspeto a imagem seguinte:

    ![](./media/virtual-network-nsg-troubleshoot-portal/image8.png)

    Confirme que é aberta ao abrir uma ligação de RDP para a VM ou ao utilizar a ferramenta de PsPing porta TCP 3389. Pode obter mais informações sobre o PsPing lendo o [PsPing a página de transferência](https://technet.microsoft.com/sysinternals/psping.aspx).

### <a name="view-effective-security-rules-for-a-network-interface"></a>Regras de segurança eficaz de vista para uma interface de rede

Se o seu fluxo de tráfego VM é afectado para uma NIC específica, pode ver uma lista completa das regras eficazes para o NIC a partir do contexto de interfaces de rede completando os passos seguintes:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**e, em seguida, clique em **interfaces de rede** na lista que é apresentada.
3. Selecione uma interface de rede. Na seguinte imagem, uma imagem com o nome *VM1 NIC1* está selecionada.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image5.png)

    Repare que o **âmbito** está definido para a interface de rede selecionada. Para saber mais sobre as informações adicionais mostradas, leia o passo 6 da secção **NSGs resolução de problemas para uma VM** deste artigo.

    >[AZURE.NOTE] Se um NSG é removido de uma interface de rede, a sub-rede NSG entra em vigor ainda no NIC determinado. Neste caso, o resultado seria Mostrar apenas as regras da sub-rede NSG. As regras aparecem apenas se o NIC estiver ligado a uma VM.

4. Pode editar diretamente regras para NSGs associados a uma imagem e uma sub-rede. Para saber como, ler o passo 8 da secção **regras de segurança eficaz de vista para uma máquina virtual** deste artigo.

## <a name="view-effective-security-rules-for-a-network-security-group-nsg"></a>Regras de segurança eficaz de vista para um grupo de segurança de rede (NSG)

Quando modificar regras NSG, poderá querer rever o impacto das regras a ser adicionado numa nomeadamente VM. Pode ver uma lista completa das regras de segurança eficaz para todos os NIC que um determinado NSG é aplicada, sem ter de mudar de contexto a pá NSG determinado. Resolver problemas de regulamentação eficaz dentro de uma NSG, conclua os passos seguintes:

1. Inicie sessão no portal do Azure em https://portal.azure.com.
2. Clique em **mais serviços**e, em seguida, clique em **grupos de segurança de rede** na lista que é apresentada.
3. Selecione um NSG. Na seguinte imagem, uma NSG denominada VM1 nsg foi selecionada.

    ![](./media/virtual-network-nsg-troubleshoot-portal/image6.png)

    Tenha em atenção as seguintes secções da imagem anterior:

    - **Âmbito:** Defina para NSG selecionada.
    - **Máquina Virtual:** Quando uma NSG é aplicado a uma sub-rede, é aplicado a todas as interfaces de rede anexadas a todos os VMs ligados à sub-rede. Esta lista mostra todos os VMs este NSG é aplicado a. Pode selecionar qualquer VM a partir da lista.

    >[AZURE.NOTE] Se um NSG é aplicada a apenas uma sub-rede vazia, VMs não serão listados. Se um NSG é aplicado a uma imagem que não está associada uma VM, essas NIC também não será listado. 
    - **Interface de rede:** Uma VM pode ter várias interfaces de rede. Pode selecionar uma interface de rede ligada para a VM selecionada.
    - **AssociatedNSGs:** Em qualquer altura, uma NIC pode ter até duas NSGs eficazes, um aplicadas a NIC e o outro à sub-rede. Apesar do âmbito está seleccionado como VM1-nsg, se o NIC tem uma sub-rede eficaz NSG, o resultado mostrará ambas as NSGs.
4. Pode editar diretamente regras para NSGs associadas a uma imagem ou sub-rede. Para saber como, ler o passo 8 da secção **regras de segurança eficaz de vista para uma máquina virtual** deste artigo.

Para saber mais sobre as informações adicionais mostradas, leia o passo 6 da secção **regras de segurança eficaz de vista para uma máquina virtual** deste artigo.

>[AZURE.NOTE] Apesar de um sub-rede e NIC cada só podem ter um que NSG aplicado aos mesmos, um NSG pode ser associado a vários NIC e várias sub-redes.

## <a name="considerations"></a>Considerações sobre

Considere os seguintes pontos quando problemas de conectividade de resolução de problemas:

- Regras NSG predefinidas serão bloquear o acesso de entrada da internet e permitir apenas VNet tráfego de entrada. Regras devem ser adicionadas explicitamente para permitir o acesso de entrada de Internet, conforme necessário.
- Se não existem NSG segurança regras a causar a falha a conectividade da rede uma VM, o problema poderá ser devido a:
    - Software de firewall em execução no interior do sistema operativo da VM
    - Encaminha configurada para eletrodomésticos virtuais ou tráfego no local. Tráfego da Internet pode ser redirecionado para no local através de túnel forçada. Uma ligação de RDP/SSH a partir da Internet para o seu VM poderá não funcionar com esta definição, dependendo de como o hardware de rede no local trata este tráfego. Leia o artigo [Resolução de problemas rotas](virtual-network-routes-troubleshoot-powershell.md) para aprender a diagnosticar os problemas de encaminhar poderão estar a impedir o fluxo de tráfego e terminar a VM. 
- Se lhe tiver dispõe VNets, por predefinição, a etiqueta VIRTUAL_NETWORK irá expandir automaticamente para incluir prefixos para VNets dispõe. Pode ver estes prefixos na lista **ExpandedAddressPrefix** , para resolver quaisquer problemas relacionados com a conectividade de efectuado VNet. 
- Regras de segurança eficaz apenas são apresentadas se existe uma NSG associada a VM NIC e ou sub-rede. 
- Se existirem sem NSGs associadas a NIC ou sub-rede e têm um endereço IP público atribuído ao seu VM, todas as portas será abertas para acesso entrada e saída. Se a VM tem um endereço IP público, aplicar NSGs a NIC ou sub-rede é vivamente recomendado.