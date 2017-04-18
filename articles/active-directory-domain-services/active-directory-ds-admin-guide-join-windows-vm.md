<properties
    pageTitle="Azure Active Directory serviços de domínio: Participar uma VM de servidor do Windows para um domínio gerido | Microsoft Azure"
    description="Aderir a uma máquina de virtual do Windows Server para serviços de domínio do Azure AD"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/02/2016"
    ms.author="maheshu"/>

# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Associar uma máquina de virtual do Windows Server a um domínio gerido

> [AZURE.SELECTOR]
- [Portal clássica Azure - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

Este artigo mostra-lhe como participar uma máquina de virtual com o Windows Server 2012 R2 para um domínio gerido dos serviços de domínio do Azure AD, utilizando o portal clássico Azure.


## <a name="step-1-create-the-windows-server-virtual-machine"></a>Passo 1: Criar a máquina virtual do Windows Server
Siga as instruções indicadas no tutorial [criar uma máquina virtual a executar o Windows no portal do Azure clássico](../virtual-machines/virtual-machines-windows-classic-tutorial.md) . É importante para se certificar de que este recentemente criado máquina virtual está ligado à rede virtual mesmo nas quais é activado dos serviços de domínio do Azure AD. A opção 'Criar rápida' não permitem-lhe aderir a máquina virtual a uma rede virtual. Por isso, tem de utilizar a opção 'A partir de galeria' para criar a máquina virtual.

Execute os passos seguintes para criar uma máquina de virtual Windows associada à rede virtual na qual tenha ativado dos serviços de domínio do Azure AD.

1. No portal clássico Azure, na barra de comandos na parte inferior da janela, clique em **Novo**.

2. **Calcular**, clique em **Máquina Virtual**, em seguida, clique na **Partir da Galeria**.

3. Primeiro ecrã permite-lhe **Escolher uma imagem** para a sua máquina virtual a partir da lista de imagens disponíveis. Selecione a imagem adequada.

    ![Selecione imagem](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Segundo ecrã permite-lhe escolher o nome do computador, tamanho e administrativo nome de utilizador e palavra-passe. Utilize a camada e o tamanho que requerem a execução da sua aplicação ou a carga de trabalho. O nome de utilizador que escolher aqui é um utilizador do local de administrador no computador. Não introduza aqui credenciais de uma conta de utilizador de domínio.

    ![Configurar máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-config.png)

5. Terceiro ecrã permite-lhe configurar recursos para funcionamento em rede, armazenamento e disponibilidade. Certifique-se de que seleciona a rede virtual na qual activou serviços de domínio do Azure AD do menu pendente de **Região/afinidade da rede grupo/Virtual** . Especifique um **Nome de serviço de nuvem DNS** conforme adequado para a máquina virtual.

    ![Selecione rede virtual para máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [AZURE.WARNING]
    Certifique-se de que aderir a máquina virtual à mesma rede virtual na qual tenha ativado dos serviços de domínio do Azure AD. Como resultado, a máquina virtual pode «ver» o domínio e efetuar tarefas, como participar o domínio. Se optar por criar a máquina virtual numa rede virtual diferente, ligar essa rede virtual à rede virtual na qual tenha ativado dos serviços de domínio do Azure AD.

6. O quarto ecrã permite-lhe instalar o agente VM e configurar algumas as extensões de disponíveis.

    ![Concluído](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)

7. Quando a máquina virtual estiver criada, o portal clássico listas a nova máquina virtual sob o nó **máquinas virtuais** . O serviço de nuvem e máquina virtual são iniciadas automaticamente tanto o estado está listado como **em execução**.

    ![Máquina virtual está a trabalhar](./media/active-directory-domain-services-admin-guide/create-windows-vm-running.png)


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>Passo 2: Ligar para o Windows Server virtual machine utilizando a conta de administrador local
Agora, vamos ligar à recentemente criada Windows Server virtual máquina, para participar na mesma para o domínio. Utilize as credenciais de administrador local que especificou quando criar máquina virtual, para ligar à mesma.

Execute os seguintes passos para ligar à máquina virtual.

1. Navegue até ao nó **máquinas virtuais** no portal do clássico. Selecione a máquina virtual que criou no passo 1 e clique em **Ligar** na barra de comandos na parte inferior da janela.

    ![Ligar a máquina de virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal clássico pede-lhe para abrir ou guardar um ficheiro com uma extensão. 'RDP', que é utilizada para ligar à máquina virtual. Clique para abrir o ficheiro quando já terminou a transferir.

3. No pedido de início de sessão, introduza as suas **credenciais de administrador local**, que especificou ao criar a máquina virtual. Por exemplo, utilizámos 'localhost\mahesh' neste exemplo.

Neste momento, devem ser iniciou sessão para a máquina de virtual Windows recentemente criada utilizando credenciais de administrador locais. O passo seguinte é aderir a máquina virtual para o domínio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>Passo 3: Associação a máquina de virtual do Windows Server para o domínio gerido AAD DS
Execute os passos seguintes para aderir a máquina virtual do Windows Server para o domínio gerido AAD DS.

1. Ligar ao Windows Server, conforme mostrado no passo 2. A partir do ecrã Iniciar, abra o **Gestor de servidor**.

2. Clique em **Local Server** no painel esquerdo da janela do Gestor de servidor.

    ![Iniciação Gestor de servidor de máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Na secção **Propriedades** , clique em **grupo de trabalho** . Na página de propriedades **Propriedades do sistema** , clique em **Alterar** para aderir ao domínio.

    ![Página de propriedades do sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

4. Especifique o nome de domínio dos seus serviços de domínio do Azure AD gerido domínio na caixa de texto **domínio** e clique em **OK**.

    ![Especifique o domínio para ser associadas](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

5. Lhe for pedido para introduzir as suas credenciais para aderir ao domínio. Certifique-se de **Especifique as credenciais para um utilizador que pertencem aos administradores CC AAD** grupo. Apenas os membros deste grupo tem privilégios para associar máquinas ao domínio gerido.

    ![Especifique as credenciais para a associação de domínio](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

6. Pode especificar as credenciais de qualquer uma das seguintes formas:

    - Formato UPN: Especifique o sufixo UPN para a conta de utilizador, tal como está configurado no Azure AD. Neste exemplo, o sufixo UPN do utilizador 'Rodrigo' é 'bob@domainservicespreview.onmicrosoft.com'.

    - Formato de SAMAccountName: pode especificar o nome da conta no formato de SAMAccountName. Neste exemplo, o utilizador 'Rodrigo' seria necessário introduzir 'CONTOSO100\bob'.

        > [AZURE.NOTE] **Recomendamos que utilize o formato UPN para especificar as credenciais.** O SAMAccountName pode ser gerados automaticamente se prefixo UPN de um utilizador for demasiado tempo (por exemplo, 'joereallylongnameuser'). Se vários utilizadores têm o mesmo prefixo UPN (por exemplo, ' João') no seu inquilino do Azure AD, o seu formato SAMAccountName pode ser de pelo serviço gerados automaticamente. Nestes casos, o formato UPN pode ser utilizado com fiabilidade para iniciar sessão no domínio.

7. Depois de associação de domínio for bem sucedida, verá a seguinte mensagem de boas-vindas para o domínio. Reinicie o computador virtual concluir a operação de associação de domínio.

    ![Bem-vindo ao domínio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Resolução de problemas de associação de domínio
### <a name="connectivity-issues"></a>Problemas de conectividade
Se a máquina virtual não conseguir encontrar o domínio, consulte os seguintes passos de resolução de problemas:

- Certifique-se de que a máquina virtual está ligada à rede virtual mesmo que a tenha ativado serviços de domínio no. Caso contrário, a máquina virtual está não é possível ligar para o domínio e, consequentemente, não consegue aderir ao domínio.

- Se estiver ligada a máquina virtual a outra rede virtual, certifique-se de que esta rede virtual está ligado à rede virtual na qual tenha ativado dos serviços de domínio.

- Tente executar o ping o domínio utilizando o nome de domínio do domínio gerido (por exemplo, ' ping contoso100.com'). Se ainda não é possível fazê-lo, tente executar o ping os endereços IP para o domínio apresentado na página de ter ativado dos serviços de domínio do Azure AD (por exemplo, ' ping 10.0.0.4'). Se não conseguir executar o ping o endereço IP, mas não o domínio, o DNS pode ser configurado incorretamente. Poderá não ter configurado os endereços IP do domínio como servidores DNS para a rede virtual.

- Experimente esvaziar cache de resolução DNS na máquina virtual (' ipconfig/flushdns').

Se obtiver à caixa de diálogo que lhe pede para introduzir as credenciais aderir ao domínio, não tiver problemas de conectividade.


### <a name="credentials-related-issues"></a>Problemas relacionados com as credenciais
Consulte os passos seguintes se estiver com problemas com as credenciais e não conseguir aderir ao domínio.

- Experimente utilizar o formato UPN para especificar as credenciais. SAMAccountName para a sua conta poderá gerados automaticamente se existem vários utilizadores com o mesmo prefixo UPN no seu inquilino ou se o prefixo UPN é demasiado longo. Por conseguinte, o formato de SAMAccountName para a sua conta pode ser diferente a partir do que pode espera ou utiliza no seu domínio no local.

- Experimente utilizar as credenciais de uma conta de utilizador que pertença ao grupo de 'AAD CC administradores' para participar máquinas para o domínio gerido.

- Certifique-se de que tem [com capacidade de sincronização de palavra-passe](active-directory-ds-getting-started-password-sync.md) em conformidade com os passos descritos no guia de introdução.

- Certifique-se de que utiliza o UPN do utilizador, tal como está configurado no Azure AD (por exemplo, 'bob@domainservicespreview.onmicrosoft.com') para iniciar sessão.

- Certifique-se de que tem aguardou o tempo suficiente para a sincronização de palavras-passe concluir conforme especificado no guia de introdução.


## <a name="related-content"></a>Conteúdo relacionado

- [Serviços de domínio Azure AD - guia de introdução](./active-directory-ds-getting-started.md)

- [Administrar um domínio gerido de serviços de domínio do Azure AD](./active-directory-ds-admin-guide-administer-domain.md)
