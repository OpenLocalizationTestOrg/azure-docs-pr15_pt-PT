<properties
    pageTitle="Azure Active Directory serviços de domínio: Administrar o DNS na domínios geridos | Microsoft Azure"
    description="Administrar DNS no Azure Active Directory Domain Services geridos domínios"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Administrar DNS num domínio gerido dos serviços de domínio do Azure AD
Azure Active Directory Domain Services inclui um servidor DNS (resolução do nome de domínio) que fornece a resolução DNS para o domínio gerido. Por vezes, poderá ter de configurar o DNS no domínio gerido. Poderá ter de criar registos DNS para computadores que não são associadas ao domínio, configurar endereços IP virtuais para balanceadores de carga ou configuração externos reencaminhadores de DNS. Por este motivo, os utilizadores que pertencem ao grupo 'AAD CC administradores' são concedidos privilégios de administração de DNS no domínio gerido.


## <a name="before-you-begin"></a>Antes de começar
Para efetuar as tarefas listadas neste artigo, precisa de:

1. Uma **subscrição do Azure**válida.

2. Um **diretório do Azure AD** - quer sincronizadas com um diretório no local ou apenas na nuvem.

3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o tiver feito, siga todas as tarefas descritas o [Guia de introdução](./active-directory-ds-getting-started.md).

4. Uma **máquina de virtual façam parte de um domínio** a partir do qual administrar os serviços de domínio do Azure AD geridas domínio. Se não tiver uma máquina de virtual, siga todas as tarefas descritas no artigo intitulado [participar uma máquina de virtual do Windows para um domínio gerido](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Tem as credenciais de uma **conta de utilizador que pertencem ao grupo ' AAD CC administradores '** no seu diretório, para administrar o DNS para o seu domínio gerido.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>Tarefa 1 - aprovisionar uma máquina de virtual façam parte de um domínio para administrar remotamente DNS para o domínio gerido
Azure domínios geridos de serviços de domínio do AD podem ser geridos remotamente utilizando familiares do Active Directory as ferramentas administrativas como o Active Directory administrativo centro (ADAC) ou AD PowerShell. Do mesmo modo, o DNS para o domínio gerido podem ser administrado remotamente utilizando as ferramentas de administração do servidor de DNS.

Os administradores no seu diretório do Azure AD não possui privilégios para ligar a controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Os membros do grupo 'AAD CC administradores' podem administrar o DNS para domínios geridos remotamente utilizando as ferramentas de servidor de DNS de um computador cliente e Server Windows que está associado ao domínio gerido. Ferramentas de servidor DNS podem ser instaladas como parte da funcionalidade opcional ferramentas de administração de servidor remoto (FARS) no Windows Server e computadores cliente associados ao domínio gerido.

A primeira tarefa é aprovisionar uma máquina de virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [participar uma máquina de virtual do Windows Server para um domínio de serviços de domínio do Azure AD geridos](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>Tarefa 2 - ferramentas de instalar o DNS Server na máquina virtual
Execute os passos seguintes para instalar as ferramentas de administração de DNS na máquina virtual domínio associado. Para mais informações sobre como [instalar e utilizar ferramentas de administração do servidor remoto](https://technet.microsoft.com/library/hh831501.aspx), consulte o artigo da Technet.

1. Navegue até ao nó **máquinas virtuais** no portal do Azure clássico. Selecione a máquina virtual que criou na tarefa 1 e clique em **Ligar** na barra de comandos na parte inferior da janela.

    ![Ligar a máquina de virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal clássico pede-lhe para abrir ou guardar um ficheiro com uma extensão. 'RDP', que é utilizada para ligar à máquina virtual. Quando terminar a transferência, clique no ficheiro.

3. No pedido de início de sessão, utilize as credenciais de um utilizador que pertencem ao grupo 'AAD CC administradores'. Por exemplo, utilizamos 'bob@domainservicespreview.onmicrosoft.com' no nosso caso.

4. A partir do ecrã Iniciar, abra o **Gestor de servidor**. Clique em **Adicionar funções e funcionalidades** no painel central da janela do Gestor de servidor.

    ![Iniciação Gestor de servidor de máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. Na página **Antes de começar** do **Assistente de funcionalidades e adicionar funções**, clique em **seguinte**.

    ![Antes de começar a página](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. Na página **Tipo de instalação** , deixe a opção de **instalação baseado em funções ou funcionalidade** selecionada e clique em **seguinte**.

    ![Página de tipo de instalação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. Na página de **Seleção de servidor** , selecione a máquina virtual atual a partir do conjunto de servidor e clique em **seguinte**.

    ![Página de seleção do servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. Na página de **Funções de servidor** , clique em **seguinte**. Vamos ignorar esta página uma vez que recomendamos que não consegue instalar quaisquer funções no servidor.

9. Na página **funcionalidades** , clique para expandir o nó de **Ferramentas de administração do servidor remoto** e, em seguida, clique em para expandir o nó de **Ferramentas de administração de funções** . Selecione a funcionalidade de **Ferramentas do servidor DNS** na lista de ferramentas de administração de funções.

    ![Página de funcionalidades](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

10. Na página de **confirmação** , clique em **instalar** para instalar a funcionalidade de ferramentas do servidor DNS na máquina virtual. Quando a instalação da funcionalidade estiver concluída com êxito, clique em **Fechar** para sair do assistente **Adicionar funções e funcionalidades** .

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)


## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>Tarefa 3 - iniciação consola de gestão de DNS para administrar DNS
Agora que a funcionalidade de ferramentas do servidor DNS estiver instalada no domínio associadas máquina virtual, podemos utilizar as ferramentas DNS para administrar DNS no domínio gerido.

> [AZURE.NOTE] Tem de ser membro do grupo 'AAD CC administradores', para administrar DNS no domínio gerido.

1. A partir do ecrã Iniciar, clique em **Ferramentas administrativas**. Deverá visualizar a consola **DNS** instalada no computador virtual.

    ![Ferramentas administrativas - consola DNS](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)

2. Clique em **DNS** para iniciar a consola de gestão de DNS.

3. Na caixa de diálogo **ligar ao servidor DNS** , clique na opção intitulada **seguinte computador**e introduza o nome de domínio DNS do domínio gerido (por exemplo, ' contoso100.com').

    ![Consola DNS - ligar ao domínio](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

4. Consola de DNS liga-se para o domínio gerido.

    ![Consola DNS - administrar o domínio](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

5. Agora pode utilizar a consola de DNS para adicionar entradas de DNS para computadores numa rede virtual na qual tenha ativado AAD serviços de domínio.

> [AZURE.WARNING] Tenha cuidado quando administrar o DNS para o domínio gerido utilizando ferramentas de administração de DNS. Certifique-se de que que **não eliminar ou modificar os registos DNS incorporados que são utilizados pelos serviços de domínio no domínio**. Registos DNS incorporados incluem registos DNS do domínio, registos de servidor de nomes e outros registos utilizados para a localização do Centro de dados. Se modificar estes registos, são interrompidos dos serviços de domínio na rede virtual.


Consulte o [artigo de ferramentas DNS no Web site Technet](https://technet.microsoft.com/library/cc753579.aspx) para obter mais informações sobre a gestão de DNS.


## <a name="related-content"></a>Conteúdo relacionado

- [Serviços de domínio Azure AD - guia de introdução](./active-directory-ds-getting-started.md)

- [Aderir a uma máquina de virtual do Windows Server para um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Administrar um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Ferramentas de administração do DNS](https://technet.microsoft.com/library/cc753579.aspx)
