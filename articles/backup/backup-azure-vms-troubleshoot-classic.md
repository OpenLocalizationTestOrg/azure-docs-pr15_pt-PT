<properties
    pageTitle="Resolver problemas de cópia de segurança do Azure máquina virtual | Microsoft Azure"
    description="Resolver problemas de cópia de segurança e restauro de máquinas virtuais Azure"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>Resolver problemas de cópia de segurança do Azure máquina virtual

> [AZURE.SELECTOR]
- [Recuperação serviços Cofre](backup-azure-vms-troubleshoot.md)
- [Cofre cópia de segurança](backup-azure-vms-troubleshoot-classic.md)

Pode resolver problemas de erros encontrados enquanto utilizar Azure cópia de segurança com informações listadas na tabela abaixo.

## <a name="discovery"></a>Deteção

| Operação de cópia de segurança | Detalhes do erro | Solução |
| -------- | -------- | -------|
| Deteção | Ocorreu uma falha ao descobrir novos itens - Microsoft Azure cópia de segurança encontrou e erro interno. Aguarde alguns minutos e, em seguida, tente novamente a operação. | Repita o processo de deteção 15 minutos depois.
| Deteção | Ocorreu uma falha ao descobrir novos itens – deteção outra operação já está em curso. Aguarde até que a operação de deteção atual foi concluída. | Nenhum |

## <a name="register"></a>Registe-se
| Operação de cópia de segurança | Detalhes do erro | Solução |
| -------- | -------- | -------|
| Registe-se | Número de discos de dados ligados à máquina virtual excedido o limite suportado - introduza desanexar alguns discos dados nesta máquina virtual e repita a operação. Cópia de segurança do Azure suporta até 16 dados discos anexados a uma máquina virtual Azure para cópia de segurança | Nenhum |
| Registe-se | Cópia de segurança do Microsoft Azure encontrou um erro interno - Aguarde alguns minutos e tente novamente a operação. Se o problema persistir, contacte o Microsoft Support. | Pode obter este erro devido a um da configuração não suportada seguinte da VM no Premium LRS. <br> Armazenamento de Premium VMs pode ser cópias de segurança utilizando recuperação serviços cofre. [Saiba mais](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| Registe-se | Ocorreu uma falha com o agente de instalar o tempo limite da operação de registo | Verifique se a versão do SO da máquina virtual é suportada. |
| Registe-se | Comando execução falhou - outra operação está em curso sobre esse item. Aguarde até que seja concluída a operação anterior | Nenhum |
| Registe-se | Máquinas virtuais tendo discos rígido virtuais armazenados no armazenamento Premium não são suportadas para cópia de segurança | Nenhum |
| Registe-se | Agente de máquina virtual não estiver presente na máquina virtual - instale requeridos pré necessários, agente VM e reinicie a operação. | [Leia mais](#vm-agent) sobre a instalação do agente VM e como validar a instalação do agente VM. |

## <a name="backup"></a>Cópia de segurança

| Operação de cópia de segurança | Detalhes do erro | Solução |
| -------- | -------- | -------|
| Cópia de segurança | Não foi possível comunicar com o agente VM para Estado instantâneo. Tarefa de sub instantâneo VM excedeu. -Consulte o guia de resolução de problemas sobre como resolver este problema. | Este erro é devolvido se existir um problema com o agente de VM ou acesso à rede a infraestrutura do Azure está bloqueado na alguma forma. Saiba mais sobre [instantâneo VM de depuração de problemas](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Se o agente VM não está a causar problemas, em seguida, reinicie a VM. Por vezes um Estado VM incorrecto pode causar problemas e reiniciar a VM repõe este "mau estado" |
| Cópia de segurança | Ocorreu uma falha com cópia de segurança com um erro interno - repita a operação de alguns minutos. Se o problema persistir, contacte o Microsoft Support | Verifique se existe um problema temporário ao aceder ao armazenamento VM. Verifique se existem [Azure Estado](https://azure.microsoft.com/en-us/status/) para ver se existe qualquer problema em curso relacionado com o cluster / / rede de armazenamento na região. Fórum é atenuada repetir o problema de cópia de segurança da mensagem. |
| Cópia de segurança | Não conseguiu executar a operação como VM já não existe. | Não pode ser executada cópia de segurança, tal como a VM configurada para cópia de segurança foi eliminada. Parar de cópias de segurança mais indo para a vista de itens protegido, selecione item protegida e clique em Parar proteção. Pode manter dados ao selecionar a opção de manter a cópia de segurança de dados. Pode retomar mais tarde a proteção para esta máquina virtual ao clicar em Configurar proteção da vista de itens registado|
| Cópia de segurança | Ocorreu uma falha ao instalar os serviços de recuperação do Azure extensão no item selecionado - VM agente é um requisito pré extensão de serviços de recuperação do Azure. Instale o agente Azure VM e reinicie a operação de registo | <ol> <li>Verifique se o agente VM ter sido instalado corretamente. <li>Certifique-se de que o sinalizador no VM config está definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre a instalação do agente VM e como validar a instalação do agente VM. |
| Cópia de segurança | Comando execução falhou - outra operação está em curso sobre esse item. Aguarde até que seja concluída a operação anterior e, em seguida, volte a tentar | Uma cópia de segurança existente ou restaurar tarefa para a VM está a ser executado e não é possível iniciar uma nova tarefa, enquanto a tarefa existente estiver em execução. |
| Cópia de segurança | Extensão falha na instalação com o erro "COM + não foi possível falar ao coordenador de transações Microsoft Distributed | Normalmente, isto significa que o serviço COM + não está em execução. Contacte o suporte da Microsoft para obter ajuda sobre corrigir este problema. |
| Cópia de segurança | Instantâneo operação Ocorreu uma falha com o erro de operação VSS "Esta unidade está bloqueada por encriptação de unidade BitLocker. Terá de desbloquear esta unidade a partir do painel de controlo. | Desativar o BitLocker para todas as unidades na VM e observe se o problema VSS é resolvido |
| Cópia de segurança | Máquinas virtuais tendo discos rígido virtuais armazenados no armazenamento Premium não são suportadas para cópia de segurança | Nenhum |
| Cópia de segurança | Azure máquina de Virtual não foi encontrado. | Isto acontece quando é eliminada a VM principal, mas continua a política de cópia de segurança procurar uma VM efectuar cópia de segurança. Para corrigir este erro: <ol><li>Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recursos [nome do serviço de nuvem] <br>(OU) <li> Desative a proteção contra para este VM para que não irão ser acionadas cópias de segurança subsequentes. </ol> |
| Cópia de segurança | Agente de máquina virtual não estiver presente na máquina virtual - instale requeridos pré necessários, agente VM e reinicie a operação. | [Leia mais](#vm-agent) sobre a instalação do agente VM e como validar a instalação do agente VM. |

## <a name="jobs"></a>Tarefas
| Operação | Detalhes do erro | Solução |
| -------- | -------- | -------|
| Cancelar tarefa | Cancelamento não é suportado para este tipo de trabalho - aguarde até que a tarefa estiver concluída. | Nenhum |
| Cancelar tarefa | A tarefa não está num Estado cancelável - Aguarde até que a tarefa estiver concluída. <br>OU<br> A tarefa selecionada não está num Estado cancelável - aguarde pela conclusão da tarefa.| Em todos os probabilidade, a tarefa de quase esteja concluída; Aguarde até que a tarefa estiver concluída |
| Cancelar tarefa | Não pode cancelar a tarefa de uma vez que não está em curso - cancelamento só é suportada para tarefas que são em curso. Cancele tentativa de um progresso no tarefa. | Isto acontece devido a um estado transitório. Aguardar um minuto e repita a operação de cancelar |
| Cancelar tarefa | Ocorreu uma falha cancelar a tarefa - Aguarde até que a tarefa é concluída. | Nenhum |


## <a name="restore"></a>Restaurar
| Operação | Detalhes do erro | Solução |
| -------- | -------- | -------|
| Restaurar | Restaurar falhou com o erro interno da nuvem | <ol><li>Serviço em nuvem para o qual está a tentar restaurar está configurado com as definições de DNS. Pode verificar <br>$deployment = get AzureDeployment - ServiceName "ServiceName"-ranhura "Produção" Get AzureDns - DnsSettings $deployment. DnsSettings<br>Se existir endereço configurado, isto significa que as definições de DNS estão configuradas.<br> <li>Serviço de nuvem para o qual está a tentar restaurar está configurado com reserva IP e VMs existentes num serviço de nuvem estão no estado parado.<br>Pode verificar que um serviço na nuvem tem reservadas IP através das seguintes cmdlets do powershell:<br>$deployment = get AzureDeployment - ServiceName "servicename"-DEP ranhura "Produção" $. ReservedIPName <br><li>Está a tentar restaurar uma máquina de virtual com configurações de rede especiais seguinte num serviço na nuvem mesmo. <br>-Máquinas virtuais em configuração do Balanceador de carga (interna e externa)<br>-Máquinas virtuais com vários IPs reservadas<br>-Máquinas virtuais com vários NIC<br>Consulte selecione um novo serviço na nuvem na IU ou consulte [Restaurar considerações](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) para VMs com configurações de rede especial</ol> |
| Restaurar | O nome DNS seleccionado já é disponibilizado - Especifique um nome diferente de DNS e tente novamente. | O nome do DNS aqui refere-se para o nome do serviço na nuvem (normalmente terminando. cloudapp.net). Isto tem de ser exclusivo. Se se deparar com este erro, tem de escolher um nome diferente de VM durante a restaurar. <br><br> Este erro é apresentado apenas para utilizadores do portal do Azure. A operação de restauro através do PowerShell com êxito porque apenas restaura discos e não cria a VM. O erro vai ser é confrontado quando a VM explicitamente é criada por si depois do disco restaurar o funcionamento. |
| Restaurar | A configuração de rede virtual especificado não está correcta - especifique uma configuração de rede virtual diferente e tente novamente. | Nenhum |
| Restaurar | O serviço de nuvem especificada está a utilizar um inquérito reservado, o que não correspondem com a configuração da máquina virtual a ser restaurada - Especifique um serviço em nuvem diferente que não está a utilizar o IP reservado ou escolher outro ponto de recuperação para restaurar a partir de. | Nenhum |
| Restaurar | Serviço em nuvem atingiu o limite num número de pontos finais entrados - repetir a operação ao especificar um serviço em nuvem diferente ou ao utilizar um ponto final existente. | Nenhum |
| Restaurar | Conta de armazenamento de cofre e destino cópia de segurança são em dois diferentes regiões - Certifique-se de que a conta de armazenamento especificada numa operação de restaurar está na mesma região Azure como o Cofre cópia de segurança. | Nenhum |
| Restaurar | Conta de armazenamento especificada para a operação de restauro não é suportada - contas de armazenamento de apenas Basic/padrão com localmente redundantes ou definições de replicação redundantes geo são suportadas. Seleccione uma conta de armazenamento suportado | Nenhum |
| Restaurar | Tipo de conta de armazenamento especificada para a operação de restauro não está online - Certifique-se de que a conta de armazenamento especificada numa operação de restaurar está online | Isto pode acontecer devido a um erro breves ou devido a uma falha de armazenamento do Windows Azure. Escolha outra conta de armazenamento. |
| Restaurar | Atingiu Quota de grupo de recursos - elimine alguns grupos de recursos a partir do Azure portal ou contacte o suporte Azure para aumentar os limites. | Nenhum |
| Restaurar | Não existe sub-rede seleccionada - seleccione uma sub-rede que existe | Nenhum |


## <a name="policy"></a>Política
| Operação | Detalhes do erro | Solução |
| -------- | -------- | -------|
| Criar a política | Ocorreu uma falha ao criar a política - reduza opções para continuar com a configuração da política de retenção. | Nenhum |


## <a name="vm-agent"></a>Agente VM

### <a name="setting-up-the-vm-agent"></a>Configurar o agente de VM
Normalmente, o agente VM já existe na VMs que são criados a partir da Galeria Azure. No entanto, máquinas virtuais que são migradas do centros de dados no local não teria o Agent VM instalado. Para tais VMs, o agente VM tem de estar instalado explicitamente. Leia mais sobre como [instalar o agente de VM numa VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para VMs do Windows:

- Transfira e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Terá privilégios de administrador para concluir a instalação.
- [Atualize a propriedade VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

Para Linux VMs:

- Instale mais recente [Linux agente](https://github.com/Azure/WALinuxAgent) a partir de github.
- [Atualize a propriedade VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.


### <a name="updating-the-vm-agent"></a>Atualizar o agente VM
Para VMs do Windows:

- Atualizar o agente VM é tão simple como reinstalar o [agente de VM binários](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, é necessário para se certificar de que nenhuma operação de cópia de segurança está em execução enquanto o agente VM está a ser atualizado.

Para Linux VMs:

- Siga as instruções no [Atualizar agente de VM Linux](../virtual-machines/virtual-machines-linux-update-agent.md).


### <a name="validating-vm-agent-installation"></a>Validar a instalação do agente de VM
Como verificar a versão do agente de VM no Windows VMs:

1. Inicie sessão para a máquina virtual Azure e navegue para a pasta *C:\WindowsAzure\Packages*. Deverá localizar o ficheiro de WaAppAgent.exe apresentar.
2. Com o botão direito no ficheiro, aceda às **Propriedades**e, em seguida, selecione o separador **Detalhes** . O campo de versão do produto deve ser 2.6.1198.718 ou superior





