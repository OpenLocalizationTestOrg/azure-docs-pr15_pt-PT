<properties
    pageTitle="Azure Active Directory serviços de domínio: Administrar um domínio gerido | Microsoft Azure"
    description="Administrar domínios geridos Azure Active Directory Domain Services"
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

# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Administrar um domínio gerido do Azure Active Directory Domain Services
Este artigo mostra-lhe como administrar um domínio gerido de serviços de domínio do Azure Active Directory (AD).


## <a name="before-you-begin"></a>Antes de começar
Para efetuar as tarefas listadas neste artigo, precisa de:

1. Uma **subscrição do Azure**válida.

2. Um **diretório do Azure AD** - quer sincronizadas com um diretório no local ou apenas na nuvem.

3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o tiver feito, siga todas as tarefas descritas o [Guia de introdução](./active-directory-ds-getting-started.md).

4. Uma **máquina de virtual façam parte de um domínio** a partir do qual administrar os serviços de domínio do Azure AD geridas domínio. Se não tiver uma máquina de virtual, siga todas as tarefas descritas no artigo intitulado [participar uma máquina de virtual do Windows para um domínio gerido](./active-directory-ds-admin-guide-join-windows-vm.md).

5. Tem as credenciais de uma **conta de utilizador que pertencem ao grupo ' AAD CC administradores '** no seu diretório, para administrar o seu domínio gerido.

<br>


## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Tarefas administrativas que pode executar um domínio gerido
Os membros do grupo 'AAD CC administradores' são concedidos privilégios de no domínio gerido ativá-las desempenhar tarefas como:

- Participar máquinas para o domínio gerido.

- Configure o GPO incorporado para os contentores 'AADDC computadores' e 'AADDC utilizadores' no domínio gerido.

- Administre DNS no domínio gerido.

- Criar e administrar personalizado unidades organizacionais (ou) no domínio gerido.

- Lucro acesso administrativo ao computadores aderido ao domínio gerido.


## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Privilégios administrativos não possui num domínio gerido
Domínio é gerido pela Microsoft, incluindo atividades como a aplicação de patches, monitorização e efetuar cópias de segurança. Assim, o domínio está bloqueado para baixo e não tiver privilégios para executar determinadas tarefas administrativas no domínio. Alguns exemplos de tarefas que não pode desempenhar estão abaixo.

- Não é concedido privilégios de administrador do domínio ou o administrador da empresa para o domínio gerido.

- Não é possível expandir o esquema do domínio gerido.

- Se conseguir ligar à controladores de domínio para o domínio gerido utilizando o ambiente de trabalho remoto.

- Não pode adicionar os controladores de domínio para o domínio gerido.


## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>Tarefa 1 - aprovisionar uma máquina de virtual do Windows Server façam parte de um domínio para administrar o domínio gerido remotamente
Azure domínios geridos de serviços de domínio do AD podem ser realizados familiares do Active Directory as ferramentas administrativas como o Active Directory administrativo centro (ADAC) ou AD PowerShell. Os administradores de inquilino não possui privilégios para ligar a controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Por conseguinte, os membros do grupo 'AAD CC administradores' podem administrar domínios geridos remotamente utilizando as ferramentas administrativas do AD a partir do computador cliente e Server Windows que está associado ao domínio gerido. Ferramentas administrativas do AD podem ser instaladas como parte da funcionalidade opcional ferramentas de administração de servidor remoto (FARS) no Windows Server e computadores cliente associados ao domínio gerido.

É o primeiro passo configurar uma máquina de virtual do Windows Server que está associada ao domínio gerido. Para obter instruções, consulte o artigo intitulado [participar uma máquina de virtual do Windows Server para um domínio de serviços de domínio do Azure AD geridos](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Administrar remotamente o domínio gerido a partir de um computador cliente (por exemplo, o Windows 10)
As instruções em utilizar este artigo uma máquina de virtual do Windows Server para administrar o DS AAD geridas domínio. No entanto, também pode optar por utilizar uma máquina de virtual de cliente (por exemplo, o Windows 10) do Windows para fazê-lo.

Pode [instalar ferramentas de administração de servidor remoto (FARS)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) num computador Windows cliente virtual ao seguir as instruções no TechNet.


## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>Tarefa 2 - ferramentas de administração do Active Directory instalar na máquina virtual
Execute os passos seguintes para instalar as ferramentas de administração do Active Directory na máquina virtual domínio associado. Consulte o artigo da Technet para obter mais [informações sobre como instalar e utilizar ferramentas de administração do servidor remoto](https://technet.microsoft.com/library/hh831501.aspx).

1. Navegue até ao nó **máquinas virtuais** no portal do Azure clássico. Selecione a máquina virtual que criou na tarefa 1 e clique em **Ligar** na barra de comandos na parte inferior da janela.

    ![Ligar a máquina de virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. O portal clássico pede-lhe para abrir ou guardar um ficheiro com uma extensão. 'RDP', que é utilizada para ligar à máquina virtual. Clique para abrir o ficheiro quando já terminou a transferir.

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

9. Na página **funcionalidades** , clique para expandir o nó de **Ferramentas de administração do servidor remoto** e, em seguida, clique em para expandir o nó de **Ferramentas de administração de funções** . Selecione a funcionalidade de **AD DS e ferramentas do AD LDS** na lista de ferramentas de administração de funções.

    ![Página de funcionalidades](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. Na página de **confirmação** , clique em **instalar** para instalar o AD e a funcionalidade de ferramentas do AD LDS na máquina virtual. Quando a instalação da funcionalidade estiver concluída com êxito, clique em **Fechar** para sair do assistente **Adicionar funções e funcionalidades** .

    ![Página de confirmação](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>Tarefa 3 - ligar e explorar o domínio gerido
Agora que as ferramentas administrativas do AD instaladas no domínio associadas máquina virtual, pode Utilizamos estas ferramentas para explorar e administrar o domínio gerido.

> [AZURE.NOTE] Tem de ser membro do grupo 'AAD CC administradores', para administrar o domínio gerido.

1. A partir do ecrã Iniciar, clique em **Ferramentas administrativas**. Deverá ver as ferramentas administrativas do AD instaladas no computador virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Clique em **Centro de administração do Active Directory**.

    ![Centro de administração do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Para explorar o domínio, clique no nome de domínio no painel à esquerda (por exemplo, ' contoso100.com'). Tenha em atenção dois contentores denominados 'AADDC computadores' e 'AADDC utilizadores' respetivamente.

    ![ADAC - domínio de vista](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Clique no contentor denominado **AADDC utilizadores** para ver todos os utilizadores e grupos pertencente ao domínio gerido. Deverá visualizar contas de utilizador e grupos a partir do seu Azure AD inquilinos Mostrar o neste contentor. Aviso neste exemplo, uma conta de utilizador para o utilizador denominado 'José' e um grupo designado 'AAD CC administradores' estão disponíveis neste contentor.

    ![ADAC - os utilizadores do domínio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Clique no contentor chamado **AADDC computadores** para ver os computadores associados a este domínio gerido. Deverá visualizar uma entrada para a máquina virtual atual, que está associada ao domínio. Contas de computador para todos os computadores que são associadas ao domínio gerido de serviços de domínio do Azure AD são armazenadas neste contentor 'AADDC computadores'.

    ![ADAC - domínio associadas computadores](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Serviços de domínio Azure AD - guia de introdução](./active-directory-ds-getting-started.md)

- [Aderir a uma máquina de virtual do Windows Server para um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-join-windows-vm.md)

- [Implementar ferramentas de administração do servidor remoto](https://technet.microsoft.com/library/hh831501.aspx)
