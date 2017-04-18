<properties
   pageTitle="Repor uma palavra-passe do Windows local quando não está instalado o agente de convidado Azure | Microsoft Azure"
   description="Como repor a palavra-passe de uma conta de utilizador do Windows local quando o agente de convidado Azure não está instalado ou funcionamento numa VM"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Como repor a palavra-passe do Windows local do Azure VM
Pode repor o Windows palavra-passe local de uma VM no Azure utilizando que o [Azure portal ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) fornecido que o agente de convidado Azure é instalado. Este método é a forma principal para repor uma palavra-passe para um VM Azure. Se se deparar com problemas com o agente de convidado Azure não responde ou a falhar instalar após carregar uma imagem personalizada, pode repor manualmente uma palavra-passe do Windows. Este artigo explica detalhadamente como repor uma palavra-passe da conta local por anexar o disco virtual origem SO VM outra. 

> [AZURE.WARNING] Utilize apenas este processo como um último recurso. Tente sempre repor uma palavra-passe utilizando o [Azure portal ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) pela primeira vez.


## <a name="overview-of-the-process"></a>Descrição geral do processo
Os passos principais para efetuar uma palavra-passe local repor para um VM do Windows no Azure quando não for possível aceder ao agente convidado Azure é da seguinte forma:

- Elimine a origem VM. Os discos virtuais são retidos.
- Anexe o disco de SO a VM de origem para outro VM dentro da sua subscrição do Azure. Este VM é referido como a VM resolução de problemas.
- Utilizando a resolução de problemas VM, crie alguns ficheiros config SO disco a VM origem.
- Desanexe disco de SO a VM a partir da VM resolução de problemas.
- Utilize um modelo de Gestor de recursos para criar uma VM, utilizando o disco virtual original.
- Quando a nova VM botas, os ficheiros de config que criar atualizam a palavra-passe do utilizador necessário.


## <a name="detailed-steps"></a>Passos detalhados
Tente sempre repor uma palavra-passe utilizando o [Azure portal ou Azure PowerShell](virtual-machines-windows-reset-rdp.md) antes de tentar os passos seguintes. Certifique-se de que tem uma cópia de segurança da sua VM antes de começar. 

1. Elimine a VM afectada no Azure portal. Eliminar a VM, elimina apenas os metadados, a referência da VM dentro do Azure. Os discos virtuais são mantidos quando é eliminada a VM:

    - Selecione a VM no portal do Azure, clique em *Eliminar*:

    ![Eliminar VM existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. Anexe o disco de SO a VM de origem para a resolução de problemas VM. A resolução de problemas VM tem de ser na mesma região como disco de SO a VM de origem (tais como `West US`):

    - Selecione a VM resolução de problemas no portal do Azure. Clique em *discos* | *anexar existente*:

    ![Anexar disco existente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    Selecione o *Ficheiro VHD* e, em seguida, selecione a conta de armazenamento que contém a sua origem VM:

    ![Selecione a conta de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    Selecione o contentor de origem. O contentor de origem é normalmente *vhds*:

    ![Selecione o contentor de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    Selecione o vhd SO para anexar. Clique em *Selecionar* para concluir o processo:

    ![Selecione o disco virtual de origem](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. Ligar para a resolução de problemas VM utilizando o ambiente de trabalho remoto e certifique-se de que está visível no disco de SO a VM origem:

    - Selecione a VM resolução de problemas no portal do Azure e clique em *Ligar*.
    - Abra o ficheiro RDP transferências. Introduza o nome de utilizador e palavra-passe da VM resolução de problemas.
    - No Explorador de ficheiros, procure o disco de dados que anexado. Se a origem VHD do VM for o disco de apenas os dados ligado para a resolução de problemas VM, deverá ser a unidade de f::

    ![Ver dados anexados disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. Criar `gpt.ini` no `\Windows\System32\GroupPolicy` na unidade da VM de origem (se existir GPT, mudar o nome para gpt.ini.bak):

    > [AZURE.WARNING] Certifique-se de que não acidentalmente cria os seguintes ficheiros em C:\Windows, a unidade de sistema operativo para a resolução de problemas VM. Crie os seguintes ficheiros na unidade de sistema operativo para a sua origem VM que está anexado como um disco de dados.

    - Adicione as seguintes linhas para o `gpt.ini` ficheiro que criou:

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![Criar GPT](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. Criar `scripts.ini` no `\Windows\System32\GroupPolicy\Machine\Scripts`. Certifique-se de pastas ocultas são apresentadas. Se for necessário, crie o `Machine` ou `Scripts` pastas.

    - Adicione as seguintes linhas a `scripts.ini` ficheiro que criou:

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![Criar scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. Criar `FixAzureVM.cmd` no `\Windows\System32` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` com os seus próprios valores:

    ```
    NET USER <username> <newpassword>
    ```

    ![Criar FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    Tem de cumprir os requisitos de complexidade de palavra-passe configurada para sua VM quando definir a palavra-passe nova.

7. No Azure portal, desligar o disco da VM resolução de problemas:

    - Selecione a VM resolução de problemas no portal do Azure, clique em *discos*.
    - Selecione o disco de dados ligado no passo 2, clique em *Desanexar*:

    ![Desligar disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. Antes de criar uma VM, obtenha o URI para o disco de SO origem:

    - Selecione a conta de armazenamento no portal do Azure, clique em *Blobs*.
    - Selecione o contentor. O contentor de origem é normalmente *vhds*:

    ![Selecione blob de conta de armazenamento](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    Selecione a sua origem VM SO VHD e clique no botão *Copiar* junto ao nome do *URL* :

    ![Copiar disco URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. Crie uma VM a partir do disco de SO a VM origem:

    - Utilize [Este modelo de Gestor de recursos do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) para criar uma VM a partir de um VHD especializado. Clique na `Deploy to Azure` botão para abrir o portal do Azure com os detalhes de transformada em modelo povoados por si.
    - Se pretender manter as definições anteriores para a VM, selecione *Editar modelo* para fornecer o seu VNet existente, sub-rede, adaptador de rede ou IP público.
    - No `OSDISKVHDURI` caixa de texto de parâmetro, colar o URI da sua origem VHD obter no passo anterior:

    ![Criar uma VM a partir de modelo](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. Depois da nova VM estiver em execução, ligar para a VM utilizar o ambiente de trabalho remoto com a nova palavra-passe que especificou na `FixAzureVM.cmd` script.

11. A partir do seu sessão remota para a nova VM, remova os seguintes ficheiros para limpar o ambiente:

    - A partir do %windir%\System32
        - remover FixAzureVM.cmd
    - A partir do %windir%\System32\GroupPolicy\Machine\
        - Remover scripts.ini
    - A partir do %windir%\System32\GroupPolicy
        - remover GPT (se GPT existia antes, e o nome alterado-lo para gpt.ini.bak, mudar o nome do ficheiro. bak de volta para o GPT)

## <a name="next-steps"></a>Próximos passos
Se ainda não é possível estabelecer ligação utilizando o ambiente de trabalho remoto, consulte o [Guia de resolução de problemas de RDP](virtual-machines-windows-troubleshoot-rdp-connection.md). O [Guia de resolução de problemas de RDP detalhado](virtual-machines-windows-detailed-troubleshoot-rdp.md) analisa métodos em vez de passos específicos de resolução de problemas. Também pode [Abrir um pedido de suporte Azure](https://azure.microsoft.com/support/options/) para obter assistência prontos a utilizar.