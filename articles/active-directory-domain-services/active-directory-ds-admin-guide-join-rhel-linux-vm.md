<properties
    pageTitle="Azure Active Directory serviços de domínio: Associar uma VM RHEL a um domínio gerido | Microsoft Azure"
    description="Aderir a uma máquina de virtual vermelho chapéu Enterprise Linux para serviços de domínio do Azure AD"
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

# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Associar uma máquina de virtual vermelho chapéu Enterprise Linux 7 a um domínio gerido
Este artigo mostra-lhe como participar uma máquina de virtual vermelho chapéu Enterprise Linux (RHEL) 7 para um domínio gerido de serviços de domínio do Azure AD.

## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Aprovisionar uma máquina de virtual vermelho chapéu Enterprise Linux
Execute os passos seguintes para Aprovisionar uma máquina de virtual RHEL 7 através do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

    ![Dashboard de portal Azure](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-dashboard.png)

2. Clique em **Novo** no painel da esquerda e escreva **Chapéu vermelho** na barra de pesquisa, conforme apresentado na captura de ecrã seguinte. As entradas para vermelho chapéu Enterprise Linux aparecem nos resultados da pesquisa. Clique em **vermelho chapéu Enterprise Linux 7.2**.

    ![Selecione RHEL nos resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-find-rhel-image.png)

3. Resultados da pesquisa no painel de **tudo o que** devem listar a imagem vermelho chapéu Enterprise Linux 7.2. Clique em **vermelho chapéu Enterprise Linux 7.2** para ver mais informações sobre a imagem de máquina virtual.

    ![Selecione RHEL nos resultados](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-select-rhel-image.png)

4. No painel de **vermelho chapéu Enterprise Linux 7.2** , deverá ver mais informações sobre a imagem de máquina virtual. Na lista pendente **selecionar um modelo de implementação** , selecione **clássica**. Em seguida, clique no botão **Criar** .

    ![Ver detalhes de imagem](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-clicked.png)

5. No painel de **Criar VM** , introduza o **Nome do anfitrião** para a nova máquina virtual. Também especificar um nome de utilizador do administrador local no campo **nome de utilizador** e uma **palavra-passe**. Também poderá optar por utilizar uma chave SSH para autenticar o utilizador administrador local. Também Selecione uma **Camada preços** para a máquina virtual.

    ![Criar VM - detalhes básicos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-basic-details.png)

6. Clique em **configuração opcional**. No painel de **config opcional** , clique em **rede**.

    ![Criar VM - configurar a rede virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-configure-vnet.png)

7. Isto reúne um painel intitulado **rede**. No painel de **rede** , clique em **Rede Virtual** para selecionar a rede virtual à qual a VM Linux deve ser implementada. Esta ação abre o painel de **Rede Virtual** . No painel de **Rede Virtual** , escolha a opção **utilizar uma rede virtual existente** . Em seguida, selecione a rede virtual na qual dos serviços de domínio do Azure AD está disponível. Neste exemplo, vamos escolha a rede virtual 'MyPreviewVNet'.

    ![Criar VM - seleccionar rede virtual](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-select-vnet.png)

8. No painel **config opcional** , clique no botão **OK** .

    ![Criar VM - rede virtual selecionada](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-vnet-selected.png)

9. Agora está pronto para criar a máquina virtual. No painel **Criar VM** , clique no botão **Criar** .

    ![Criar VM - pronto](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm.png)

10. Implementação da nova máquina virtual com base na imagem RHEL 7.2 deverá começar.

  ![Criar VM - implementação iniciado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployment-started.png)

11. Depois de alguns minutos, a máquina virtual deve ser implementada com êxito e pronto para utilização.

  ![Criar VM - implementado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-create-vm-deployed.png)



## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Ligar remotamente à máquina de virtual Linux aprovisionada recentemente
A máquina de virtual RHEL 7.2 ter sido aprovisionada no Azure. A tarefa seguinte é ligar remotamente à máquina virtual.

**Ligar à máquina RHEL 7.2 virtual** Siga as instruções no artigo [como iniciar sessão para uma máquina de virtual com Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

O resto dos passos partem do pressuposto de que utilizar o cliente betumes SSH para ligar à máquina RHEL virtual. Para mais informações, consulte a [página de transferência betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

1. Abra o programa para.

2. Introduza o **Nome do anfitrião** para a máquina de virtual RHEL recentemente criada. Neste exemplo, o nossa máquina virtual tem o nome do anfitrião 'contoso rhel.cloudapp .net'. Se não tiver a certeza do nome do anfitrião da sua VM, consulte o dashboard VM no portal do Azure.

    ![Ligar betumes](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-connect.png)

3. Inicie sessão no computador virtual utilizando as credenciais de administrador local que especificou quando a máquina virtual foi criada. Neste exemplo, utilizámos conta de administrador local "mahesh".

    ![Início de sessão para](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-login.png)


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar pacotes necessários na máquina Linux virtual
Depois de ligar à máquina virtual, a tarefa seguinte consiste em instalar pacotes necessários para o domínio participar na máquina virtual. Execute os seguintes passos:

1. **Instalar realmd:** O pacote de realmd é utilizado para associação de domínio. No seu terminal para, escreva o seguinte comando:

    sudo yum instalar realmd

    ![Instalar realmd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-realmd.png)

    Depois de alguns minutos, o pacote de realmd deve obter instalado no computador virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-installed.png)

3. **Instalar sssd:** O pacote de realmd depende sssd para efetuar operações de associação de domínio. No seu terminal para, escreva o seguinte comando:

    sudo yum instalar sssd

    ![Instalar sssd](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-sssd.png)

    Depois de alguns minutos, o pacote de sssd deve obter instalado no computador virtual.

    ![realmd instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-sssd-installed.png)

4. **Instalar kerberos:** No seu terminal para, escreva o seguinte comando:

    sudo yum instalar estação de trabalho krb5 krb5 efectuar

    ![Instalar kerberos](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-install-kerberos.png)

    Depois de alguns minutos, o pacote de realmd deve obter instalado no computador virtual.

    ![Kerberos instalado](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kerberos-installed.png)


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Associar a máquina de virtual Linux ao domínio gerido
Agora que os pacotes necessários são instalados no computador virtual Linux, a tarefa seguinte é aderir a máquina virtual para o domínio gerido.

1. Descubra o domínio gerido AAD serviços de domínio. No seu terminal para, escreva o seguinte comando:

    descobrir o sudo realm CONTOSO100.COM

    ![Descobrir o realm](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-discover.png)

    Se **descobrir realm** não é possível localizar o seu domínio gerido, certifique-se de que o domínio está acessível a partir de máquina virtual (experimente ping). Também Certifique-se de que a máquina virtual facto foi implementada à mesma rede virtual na qual o domínio gerido está disponível.

2. Iniciar kerberos. No seu terminal para, escreva o seguinte comando. Certifique-se de que especificar um utilizador que pertença ao grupo 'AAD CC administradores'. Apenas estes utilizadores podem participar computadores para o domínio gerido.

    kinitbob@CONTOSO100.COM

    ![Kinit](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-kinit.png)

    Certifique-se de que especifique o nome de domínio para letras maiúsculas, else kinit falha.

3. Participar na máquina para o domínio. No seu terminal para, escreva o seguinte comando. Especifique o mesmo utilizador que especificou no passo anterior ('kinit').

    associação de realm sudo – verboso CONTOSO100.COM -U'bob@CONTOSO100.COM'

    ![Associação realm](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-realmd-join.png)

Deve obter uma mensagem ("com êxito inscrito máquina no realm") quando o computador com êxito está associado ao domínio gerido.


## <a name="verify-domain-join"></a>Verifique se participar de domínio
Pode rapidamente verificar se o computador foi associado com êxito para o domínio gerido. Ligar para o recentemente domínio associadas VM RHEL SSH e uma conta de utilizador do domínio e em seguida, selecione a utilizar para ver se a conta de utilizador é resolvida corretamente.

1. No seu terminal para, escreva o seguinte comando para ligar para o recentemente domínio associadas máquina de virtual RHEL utilizando SSH. Utilizar uma conta do domínio que pertence o domínio gerido (por exemplo, 'bob@CONTOSO100.COM' neste caso.)

    SSH -l bob@CONTOSO100.COM contoso rhel.cloudapp.net

2. No seu terminal para, escreva o seguinte comando para ver se o diretório foi inicializado corretamente.

    pwd

3. No seu terminal para, escreva o seguinte comando para ver se os membros do grupo estão a ser resolvidos corretamente.

    ID

Segue-se um exemplo de resultado destes comandos:

![Verifique se participar de domínio](./media/active-directory-domain-services-admin-guide/rhel-join-azure-portal-putty-verify-domain-join.png)


## <a name="troubleshooting-domain-join"></a>Resolução de problemas de associação de domínio
Consulte o artigo [resolução de problemas associação de domínio](active-directory-ds-admin-guide-join-windows-vm.md#troubleshooting-domain-join) .


## <a name="related-content"></a>Conteúdo relacionado
- [Serviços de domínio Azure AD - guia de introdução](./active-directory-ds-getting-started.md)

- [Aderir a uma máquina de virtual do Windows Server para um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Como iniciar sessão para uma máquina de virtual com Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md).

- [Instalar o Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)

- [Chapéu vermelho Enterprise Linux 7 - guia de integração do Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
