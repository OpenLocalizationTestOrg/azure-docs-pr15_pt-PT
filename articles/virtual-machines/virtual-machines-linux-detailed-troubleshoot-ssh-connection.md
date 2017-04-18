<properties
    pageTitle="Detalhadas SSH resolução de problemas para um VM Azure | Microsoft Azure"
    description="Mais detalhadas SSH passos para problemas de ligação a uma máquina virtual Azure resolução de problemas"
    keywords="SSH ligação recusado, ssh erro, azure ssh, SSH falha na ligação"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>Passos de resolução de problemas de SSH detalhados

Existem vários motivos possíveis para o cliente SSH poderá não conseguir alcançar o serviço SSH na VM. Se tiverem seguidas através dos [passos de resolução de problemas gerais SSH](virtual-machines-linux-troubleshoot-ssh-connection.md)de mais, terá de resolver o problema de ligação. Este artigo orienta-os passos detalhados de resolução de problemas para determinar onde a ligação SSH está a falhar e como para resolver a situação.

## <a name="take-preliminary-steps"></a>Tomar passos introdutórios

O diagrama seguinte mostra os componentes que estão, normalmente.

![Diagrama que mostra os componentes do serviço SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Os passos seguintes ajudá-lo a isolar a origem da falha e Descubra soluções.

Em primeiro lugar, verifique o estado da VM no portal.

No [portal do Azure](https://portal.azure.com):

1. Para VMs criados utilizando o modelo clássico de implementação, selecione **Procurar** > **máquinas virtuais (clássico)** > *nome VM*.

    -OU-

    Para VMs criadas utilizando o modelo de Gestor de recursos, selecione **Procurar** > **máquinas virtuais** > *nome VM*.

    O painel de estado para a VM deve mostrar **em execução**. Desloque para baixo para mostrar atividade recentes para cluster, armazenamento e recursos de rede.

2. Selecione **Definições** examinar os pontos finais, endereços IP e outras definições.

    Para identificar os pontos finais VMs que foram criadas utilizando o Gestor de recursos, certifique-se de que tenha sido definida um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) . Também pode verificar que as regras tenham sido aplicadas ao grupo de segurança de rede e que está a referenciados na sub-rede.

No [portal clássica Azure](https://manage.windowsazure.com), para VMs que foram criadas utilizando o modelo clássico de implementação:

1. Selecione **máquinas virtuais** > *nome VM*.
2. Seleccione a VM **Dashboard** para verificar o seu estado.
3. Selecione **Monitor** para ver atividade recente para cluster, armazenamento e recursos de rede.
4. Selecione **os pontos finais** para se certificar de que existe um ponto final para o tráfego de SSH.

Para verificar a conectividade da rede, verifique os pontos finais configurados e se pode chegar a VM através de outro protocolo, tal como HTTP ou outro serviço.

Depois destes passos, tente ligar SSH novamente.


## <a name="find-the-source-of-the-issue"></a>Encontrar a origem do problema

O cliente SSH no seu computador pode falhar alcançar o serviço SSH na VM Azure devido a problemas ou procurarem no seguinte:

- [Computador do cliente SSH](#source-1-ssh-client-computer)
- [Dispositivo de limite de organização](#source-2-organization-edge-device)
- [Ponto final de serviço em nuvem e aceder a lista de controlo (ACL)](#source-3-cloud-service-endpoint-and-acl)
- [Grupos de segurança de rede](#source-4-network-security-groups)
- [Baseado em Linux VM Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Origem 1: SSH computador cliente

Para eliminar o seu computador como a origem da falha, certifique-se de que pode fazer SSH ligações para outra no local, Linux com base no computador.

![Diagrama que realça SSH componentes do computador cliente](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a ligação falhar, verifique o seguinte procedimento no seu computador:

- Uma definição de local firewall que está a bloquear o tráfego SSH de entrada ou saído (TCP 22)
- Localmente instalado o software de proxy do cliente que está a impedir SSH ligações
- Instalado localmente monitorização software que está a impedir SSH ligações de rede
- Outros tipos de software de segurança que monitorizar o tráfego ou permitirem/não permitir tipos específicos de tráfego

Se aplicar uma destas condições, temporariamente desativar o software e tente uma ligação de SSH para um computador local para saber o motivo pelo qual que está a ser bloqueada a ligação no seu computador. Em seguida, trabalhe com o seu administrador de rede para corrigir as definições de software para permitir ligações SSH.

Se estiver a utilizar a autenticação de certificados, verifique se tem estas permissões para a pasta .ssh no seu diretório:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*. pub
- Chmod 600 ~/.ssh/id_rsa (ou outros ficheiros que tenham as suas chaves privadas armazenadas no-los)
- ~/.Ssh/known_hosts chmod 644 (contém anfitriões que ligou à via SSH)

## <a name="source-2-organization-edge-device"></a>Origem 2: Dispositivo de limite de organização

Para eliminar o seu dispositivo de margem da organização como a origem da falha, certifique-se de que o computador que está diretamente ligado à Internet pode faça SSH ligações para sua VM Azure. Se está a aceder à VM através de um site para o site VPN ou uma ligação do Azure ExpressRoute, avance para o [origem 4: grupos de segurança de rede](#nsg).

![Diagrama que realça o dispositivo de limite de organização](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se não tiver um computador que está diretamente ligado à Internet, crie uma nova VM do Azure na sua própria grupo de recursos ou serviço em nuvem e utilizá-la. Para mais informações, consulte o artigo [criar uma máquina virtual com Linux no Azure](virtual-machines-linux-quick-create-cli.md). Elimine o grupo de recursos ou o serviço VM e nuvem quando tiver concluído os testes.

Se pode criar uma ligação de SSH com um computador que está diretamente ligado à Internet, verifique o seu dispositivo de limite de organização para:

- Uma firewall interna que está a bloquear o tráfego SSH com Internet
- Um servidor proxy que está a impedir SSH ligações
- Deteção de intrusos ou monitorização software a ser executado em dispositivos na sua rede de limite que está a impedir SSH ligações de rede

Trabalhar com o seu administrador de rede para corrigir as definições dos seus dispositivos de limite de organização para permitir o tráfego SSH com Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem 3: Ponto final de serviço de nuvem e ACL

> [AZURE.NOTE] Esta origem só se aplica a VMs que foram criadas ao utilizar o modelo de implementação clássica. Para VMs que foram criadas utilizando o Gestor de recursos, avance para o [da origem de 4: grupos de segurança de rede](#nsg).

Para eliminar o ponto final de serviço de nuvem e ACL como a origem da falha, certifique-se de que outra VM Azure na mesma rede virtual pode fazer SSH ligações para sua VM.

![Diagrama que realça o ponto final de serviço de nuvem e ACL](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se não tiver VM outro na mesma rede virtual, pode facilmente criar um novo. Para mais informações, consulte o artigo [criar uma VM Linux no Azure utilizando o clip](virtual-machines-linux-quick-create-cli.md). Elimine a VM extra quando tiver terminado com os testes.

Se pode criar uma ligação de SSH com uma VM na mesma rede virtual, verifique o seguinte:

- **A configuração de ponto final para o tráfego de SSH no destino VM.** A porta TCP privada do ponto final deve corresponder a porta TCP no qual o serviço SSH na VM está a escutar. (A porta predefinida é 22). Para VMs criados utilizando o modelo de implementação do Gestor de recursos, verifique se o número da porta SSH TCP no portal do Azure ao selecionar **Procurar** > **máquinas virtuais (v2)** > *nome VM* > **Definições** > **pontos finais**.

- **ACL para o ponto final de tráfego SSH na máquina virtual destino.** Uma ACL permite-lhe especificar permitidas ou negadas tráfego da Internet, com base no respetivo endereço IP de origem. Não foram corretamente configuradas ACL podem impedir que SSH tráfego para o ponto final. Verifique a sua ACL para se certificar de que o tráfego recebido dos endereços IP públicos do seu proxy ou outros servidor edge é permitido. Para mais informações, consulte o artigo [sobre como aceder à rede listas de controlo (ACL)](../virtual-network/virtual-networks-acl.md).

Para eliminar o ponto final como uma origem do problema, remova o ponto final atual, criar um novo ponto final e especifique o nome SSH (porta TCP 22 para o número da porta públicas e privadas). Para mais informações, consulte o artigo [configurar os pontos finais num computador virtual no Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>Origem de 4: Grupos de segurança de rede

Grupos de segurança de rede permitem-lhe ter mais granular, controlo de tráfego de entrada e saída permitido. Pode criar regras que expandem subredes e na nuvem serviços numa rede virtual Azure. Verifique as suas regras de grupo de segurança de rede para se certificar de que é permitido SSH tráfego para e da Internet.
Para mais informações, consulte o artigo [sobre os grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

## <a name="source-5-linux-based-azure-virtual-machine"></a>Origem de 5: Baseado em Linux Azure máquina virtual

A última origem dos possíveis problemas é a Azure máquina de virtual propriamente dito.

![Diagrama que realça baseado em Linux Azure máquina de virtual](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se ainda não o tiver feito, siga as instruções [para repor uma palavra-passe ou SSH para baseado em Linux máquinas virtuais](virtual-machines-linux-classic-reset-access.md).

Tente ligar novamente a partir do computador. Se continuar a falhar, seguem-se alguns dos problemas possíveis:

- O serviço SSH não está em execução no computador de virtual de destino.
- O serviço SSH não está a escutar porta TCP 22. Para testar esta situação, instale um cliente telnet no seu computador local e execute "telnet *cloudServiceName*. cloudapp.net 22". Este procedimento determina se a máquina virtual permite a comunicação de entrada e saída para o ponto final SSH.
- A firewall local na máquina virtual destino tem regras que estão a impedir a entrada ou saído tráfego SSH.
- Deteção de intrusos ou monitorização software que está a ser executado na máquina virtual Azure da rede está a impedir SSH ligações.


## <a name="additional-resources"></a>Recursos adicionais
Para mais informações sobre resolução de problemas de acesso de aplicação, consulte o artigo [resolução de problemas acesso a uma aplicação em execução numa máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)