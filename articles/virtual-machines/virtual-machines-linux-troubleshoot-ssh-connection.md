<properties
    pageTitle="Resolução de problemas de ligação SSH para um VM | Microsoft Azure"
    description="Como resolver problemas como 'Falha na ligação de SSH' ou 'Ligação SSH recusado' para uma VM Azure executar Linux."
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
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Resolver problemas de SSH ligações para um Azure Linux VM falhar, erros de saída, ou for recusado
Existem várias razões que encontrar erros de Shell seguro (SSH), falhas de ligação SSH, ou SSH for recusado quando tenta ligar a uma máquina de virtual Linux (VM). Este artigo ajuda-o a localizar e corrigir os problemas. Pode utilizar o portal do Azure, Azure clip ou extensão do Access VM para Linux para resolver problemas e resolver problemas de ligação.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se precisar de mais ajuda em qualquer ponto neste artigo, pode contactar os Azure especialistas no [MSDN Azure e pilha de conteúdo adicional nos fóruns](http://azure.microsoft.com/support/forums/). Em alternativa, pode preencher um incidente de suporte Azure. Ir para o [site de suporte do Azure](http://azure.microsoft.com/support/options/) e selecione **obter suporte**. Para obter informações sobre como utilizar o Azure de suporte, leia as [FAQ de suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).


## <a name="quick-troubleshooting-steps"></a>Resolução de problemas de passos rápidos
Após cada etapa de resolução de problemas, experimente voltar a ligar para a VM.

1. Repor a configuração de SSH.
2. Repor as credenciais do utilizador.
3. Certifique-se de que as regras de [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) permitam o tráfego SSH.
    - Certifique-se de que existe uma regra de grupo de segurança de rede para permitir o tráfego SSH (por predefinição, a porta TCP 22).
    - Não é possível utilizar o redirecionamento de porta / mapeamento sem utilizar um balanceador de carga Azure.
4. Verificar o [Estado de funcionamento do recurso VM](../resource-health/resource-health-overview.md). 
    - Certifique-se de que a VM relatórios como sendo saudável.
    - Se tiver diagnósticos de arranque ativados, verifique se que a VM não é a identificação de erros de arranque nos registos do.
5. Reinicie a VM.
6. Implementar a VM.

Continue a ler para obter passos de resolução de problemas mais detalhados e explicações.


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Métodos disponíveis para resolver problemas de ligação SSH

Pode repor credenciais ou configuração SSH utilizando um dos seguintes métodos:

- [Azure portal](#using-the-azure-portal) - excelente se precisar de repor rapidamente a configuração de SSH ou SSH chave e não tem o Azure tools instalado.
- [Azure clip comandos](#using-the-azure-cli) - se de que já estejam na linha de comandos, rapidamente repor a configuração de SSH ou credenciais.
- [Extensão do azure VMAccessForLinux](#using-the-vmaccess-extension) - crie e reutilizar ficheiros de definição de json para repor as credenciais de utilizador ou de configuração de SSH.

Após cada etapa de resolução de problemas, experimente ligar à sua VM novamente. Se ainda não consigo ligar, experimente o passo seguinte.


## <a name="using-the-azure-portal"></a>Utilizar o portal do Azure
Portal do Azure fornece uma forma rápida de repor as credenciais de utilizador ou de configuração de SSH sem instalar quaisquer ferramentas no seu computador local.

Selecione o seu VM no portal do Azure. Desloque para baixo para a secção **suporte + resolução de problemas** e selecione **Repor palavra-passe** tal como no exemplo seguinte:

![Repor SSH configuração ou credenciais no portal do Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Repor a configuração de SSH
Como primeiro passo, selecione `Reset SSH configuration only` no menu pendente do **modo de** tal como a captura de ecrã anterior, em seguida, clique no botão **Repor** . Quando esta ação estiver concluída, tente aceder novamente ao seu VM.

### <a name="reset-ssh-credentials-for-a-user"></a>Repor as credenciais SSH para um utilizador
Para repor as credenciais de um utilizador existente, selecione `Reset SSH public key` ou `Reset password` no menu pendente de **modo** tal como a captura de ecrã anterior. Especificar o nome de utilizador e uma tecla SSH ou nova palavra-passe e, em seguida, clique no botão **Repor** .

Também pode criar um utilizador com privilégios de sudo no VM a partir deste menu. Introduza um novo nome de utilizador e palavra-passe associada ou SSH chave e, em seguida, clique no botão **Repor** .


## <a name="using-the-azure-cli"></a>Utilizar o clip Azure
Se ainda não o fez, [instale o clip do Azure e ligar à sua subscrição do Azure](../xplat-cli-install.md). Certifique-se de que utilizar o modo de Gestor de recursos da seguinte forma:

```
azure config mode arm
```

Se tiver criado e carregados uma imagem do disco Linux personalizada, certifique-se [No Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) versão 2.0.5 ou posterior instalado. Para VMs criados com imagens da galeria, esta extensão do access já é instalado e configurado para si.

### <a name="reset-ssh-configuration"></a>Repor a configuração de SSH
A configuração do SSHD pode ser mal configurada ou o serviço de encontrou um erro. Pode repor SSHD para se certificar de que a configuração de SSH propriamente dito é válida. Reposição SSHD deve ser o primeiro passo de resolução de problemas que tomar.

O exemplo seguinte repõe SSHD numa VM denominada `myVM` no grupo de recursos com o nome `myResourceGroup`. Utilize o seus próprio nomes de grupo VM e de recursos da seguinte forma:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Repor as credenciais SSH para um utilizador
Se for apresentada SSHD funcione corretamente, pode repor a palavra-passe para um utilizador contratantes. O exemplo seguinte repõe as credenciais para `myUsername` para o valor especificado no `myPassword`, no VM denominada `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se utilizar a autenticação por chave SSH, pode repor a chave SSH para um determinado utilizador. O exemplo seguinte atualiza a chave SSH armazenada no `~/.ssh/azure_id_rsa.pub` para o utilizador com o nome `myUsername`, no VM denominada `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>Utilizando a extensão de VMAccess
Extensão de acesso a VM para Linux lê num ficheiro de json que define ações para realizar. Estas ações incluem reposição SSHD, uma tecla SSH a reposição ou adicionar um utilizador. Pode continuar a utilizar o clip do Azure para chamar a extensão de VMAccess, mas pode reutilizar os ficheiros de json através de vários VMs se pretender. Esta abordagem permite-lhe criar um repositório de ficheiros de json que pode ser chamado para dado cenários.

### <a name="reset-sshd"></a>Repor SSHD
Criar um ficheiro com o nome `PrivateConf.json` com o seguinte conteúdo:

```bash
{  
    "reset_ssh":"True"
}
```

Utilizar o clip do Azure, em seguida, ligar para o `VMAccessForLinux` extensão para repor a sua ligação de SSHD ao especificar o seu ficheiro json. O exemplo seguinte repõe SSHD no VM denominada `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Repor as credenciais SSH para um utilizador
Se for apresentada SSHD funcione corretamente, pode repor as credenciais para um utilizador contratantes. Para repor a palavra-passe para um utilizador, crie um ficheiro denominado `PrivateConf.json`. O exemplo seguinte repõe as credenciais para `myUsername` para o valor especificado no `myPassword`. Introduza as seguintes linhas para o seu `PrivateConf.json` de ficheiros, utilizando os seus próprios valores:

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

Ou para repor a chave SSH para um utilizador, crie um ficheiro denominado `PrivateConf.json`. O exemplo seguinte repõe as credenciais para `myUsername` para o valor especificado no `myPassword`, no VM denominada `myVM` no `myResourceGroup`. Introduza as seguintes linhas para o seu `PrivateConf.json` de ficheiros, utilizando os seus próprios valores:

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Depois de criar o seu ficheiro json, utilizar o clip do Azure para ligar para o `VMAccessForLinux` extensão para repor as credenciais de utilizador SSH ao especificar o seu ficheiro json. O exemplo seguinte repõe credenciais no VM denominada `myVM` no `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>Reiniciar uma VM
Se tiver repor as credenciais de utilizador e de configuração de SSH ou encontrou um erro na fazê-lo, pode tentar reiniciar a VM para o endereço subjacentes cluster problemas.

### <a name="azure-portal"></a>Portal do Azure
Para reiniciar uma VM através do portal Azure, selecione a VM e clique na ***reiniciar o** botão tal como no exemplo seguinte:

![Reiniciar uma VM no portal do Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Clip Azure
O exemplo seguinte reinicia VM denominada `myVM` no grupo de recursos com o nome `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Implementar uma VM
Pode implementar uma VM para outro nó dentro Azure, que pode corrigir problemas de redes subjacentes. Para obter informações sobre Reimplementar uma VM, consulte o artigo [Implementar máquina virtual para novo nó Azure](virtual-machines-windows-redeploy-to-new-node.md).

> [AZURE.NOTE] Depois de concluir esta operação, dados do disco efémeras serão perdidos e endereços IP dinâmicos que estão associados a máquina virtual serão atualizados.

### <a name="azure-portal"></a>Portal do Azure
Para implementar uma VM através do portal Azure, selecione o seu VM e desloque-se até à secção **suporte + resolução de problemas** . Clique no botão **voltar a implementar** tal como no exemplo seguinte:

![Implementar uma VM no portal do Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Clip Azure
O exemplo seguinte redeploys VM denominada `myVM` no grupo de recursos com o nome `myResourceGroup`. Utilize os seus próprios valores da seguinte forma:

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>VMs criados utilizando o modelo de implementação clássico

Experimente estes passos para resolver as falhas de ligação SSH mais comuns para VMs que foram criadas ao utilizar o modelo de implementação clássico. Após cada etapa, experimente voltar a ligar para a VM.

- Repor a partir do [Azure portal](https://portal.azure.com)de acesso remoto. No portal do Azure, selecione a VM e clique no botão **Repor remota...** .

- Reinicie a VM. No [portal do Azure](https://portal.azure.com), selecione a VM e clique no botão **reiniciar** .

    -OU-

    No [portal clássica Azure](https://manage.windowsazure.com), selecione **máquinas virtuais** > **instâncias** > **reiniciar**.

- Implementar a VM para um novo nó Azure. Para obter informações sobre como implementar uma VM, consulte o artigo [Implementar máquina virtual para novo nó Azure](virtual-machines-windows-redeploy-to-new-node.md).

    Depois de concluir esta operação, dados do disco efémeras serão perdidos e endereços IP dinâmicos que estão associados a máquina virtual serão atualizados.

- Siga as instruções no artigo [como repor uma palavra-passe ou SSH para baseado em Linux máquinas virtuais](virtual-machines-linux-classic-reset-access.md) :
    - Repor a palavra-passe ou SSH chave.
    - Crie uma conta de utilizador _sudo_ .
    - Repor a configuração de SSH.

- Verificar o estado de funcionamento da VM recurso quaisquer problemas de plataforma.<br>
  Selecione o seu VM e desloque-se para baixo **Definições** > **Verificar o estado de funcionamento**.


## <a name="additional-resources"></a>Recursos adicionais

- Se estiver continua a não conseguir SSH para sua VM depois de seguir os passos após, consulte o artigo [passos de resolução de problemas mais detalhadas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) para rever os passos adicionais para resolver o problema.

- Para mais informações sobre resolução de problemas de acesso de aplicação, consulte o artigo [resolução de problemas acesso a uma aplicação em execução numa máquina virtual Azure](virtual-machines-linux-troubleshoot-app-connection.md)

- Para mais informações sobre máquinas virtuais que foram criadas utilizando o modelo de implementação clássico de resolução de problemas, consulte o artigo [como repor uma palavra-passe ou SSH para baseado em Linux máquinas virtuais](virtual-machines-linux-classic-reset-access.md).
