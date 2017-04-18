<properties
   pageTitle="Cópia de segurança de VM Azure falhar: não foi possível comunicar com o agente VM para Estado instantâneo - excedeu a tarefa de sub instantâneo VM | Microsoft Azure"
   description="Sintomas causas e resoluções para falhas de cópia de segurança Azure VM relacionados com não foi possível comunicar com o agente VM para Estado instantâneo. Tarefa de sub instantâneo VM excedida erro"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="cfreeman"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jimpark; markgal;genli"/>

# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Cópia de segurança de VM Azure falhar: não foi possível comunicar com o agente VM para Estado instantâneo - excedeu a tarefa de sub VM instantâneo

## <a name="summary"></a>Resumo

Depois de registar e agendar uma Máquina Virtual (VM) para cópia de segurança do Azure, o serviço de cópia de segurança do Azure inicia a tarefa de cópia de segurança na hora agendada por comunicar com a extensão de cópia de segurança na VM para tirar um instantâneo em qualquer altura. Existem condições que podem impedir que o instantâneo acionou que leva a uma falha de cópia de segurança. Este artigo fornece passos de resolução de problemas para problemas relacionados com falhas de cópia de segurança Azure VM relacionados com erro de tempo limite instantâneo.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintoma

Cópia de segurança do Microsoft Azure para infraestrutura como um serviço (IaaS) VM falha e devolve os detalhes desse erro da tarefa no [portal do Azure](https://portal.azure.com/)a seguinte mensagem de erro:

**Não foi possível comunicar com o agente VM para Estado instantâneo - excedeu a tarefa de sub instantâneo VM.**

## <a name="cause"></a>Causa
Existem quatro as causas mais comuns para este erro:

- A VM não tem acesso à Internet.
- O agente do Microsoft Azure VM instalado na VM é desatualizado (para Linux VMs).
- Falha a extensão de cópia de segurança atualizar ou carregar.
- Não é possível obter o estado de instantâneos ou os instantâneos não podem ser encaminhados.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Causa 1: A VM não tem acesso à Internet
Pelo requisito de implementação, a VM tenha sem acesso à Internet ou tem restrições num local que impedem o acesso a infraestrutura do Azure.

A extensão de cópia de segurança necessita de conectividade para os endereços IP públicos Azure a funcionar corretamente. Esta é uma vez que envia comandos para um ponto final de armazenamento do Windows Azure (URL de HTTP) para gerir os instantâneos da VM. Se a extensão não tem acesso à Internet público, a cópia de segurança eventualmente irá falhar.

### <a name="solution"></a>Solução
Neste cenário, utilize um dos métodos seguintes para resolver o problema:

- Intervalos de IP lista branca o Centro de dados do Azure
- Criar um caminho para o tráfego de HTTP fluxo

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>Lista branca o Azure intervalos de IP do Centro de dados

1. Obter a [lista de centro de dados do Azure IPs](https://www.microsoft.com/download/details.aspx?id=41653) para ser whitelisted.
2. Desbloquear a IPs utilizando o cmdlet NetRoute novo. Execute este cmdlet na VM Azure numa janela do PowerShell elevada (executar como administrador).
3. Adicione regras para o grupo de segurança de rede (NSG) se tiver um para permitir o acesso para a IPs.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Para criar um caminho para o tráfego de HTTP fluxo

1. Se tiver restrições de rede no local (por exemplo, NSG), implemente um servidor de proxy HTTP para encaminhar o tráfego.
2. Se tiver um grupo de segurança de rede (NSG), adicione regras para permitir o acesso à Internet a partir do proxy HTTP.

Saiba como [configurar um servidor proxy HTTP para cópias de segurança VM](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Causa 2: O agente do Microsoft Azure VM instalado na VM está desatualizado (para Linux VMs)

### <a name="solution"></a>Solução
Mais relacionado com o agente relacionados com a extensão falhas de ou para Linux VMs são causadas por problemas que afetam a um agente VM antigo. Regra geral, os primeiros passos para resolver este problema são as seguintes:

1. [Instalar o agente Azure VM mais recente](https://github.com/Azure/WALinuxAgent).
2. Certifique-se de que o Azure agent está em execução na VM. Para fazer isto, execute o seguinte comando:```ps -e```

    Se este processo não está em execução, utilize os comandos seguintes com essa indicação.

    Para Ubuntu:```service walinuxagent start```

    Para outras distribuições: ' ' waagent início do serviço
```

3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.

    We require the following logs from the customer’s VM:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:

    Enable verbose logging (y|n)

2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension

1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| ----- | ----- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. | Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. | If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. | It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. | If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
|The VM cannot get host/fabric address from DHCP.|DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).|
