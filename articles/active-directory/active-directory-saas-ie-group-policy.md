<properties
    pageTitle="Como implementar a extensão de painel de acesso para o Internet Explorer utilizando a política de grupo | Microsoft Azure"
    description="Como utilizar a política de grupo para implementar o suplemento do Internet Explorer para o portal das minhas aplicações."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/16/2016"
    ms.author="markvi"/>

#<a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como implementar a extensão de painel de acesso para o Internet Explorer utilizando a política de grupo

Este tutorial mostra como utilizar a política de grupo para remotamente instale a extensão de painel de acesso para o Internet Explorer nos computadores dos seus utilizadores. Esta extensão é necessário para utilizadores do Internet Explorer que precisam de iniciar sessão nas aplicações que podem são configuradas [com base em palavra-passe serviço single sign-on](active-directory-appssoaccess-whatis.md#password-based-single-sign-on).

Recomenda-se que os administradores automatizar a implementação desta extensão. Caso contrário, os utilizadores terão de transferir e instalar a extensão de si, que está sujeito às erro de utilizador e requer permissões de administrador. Neste tutorial abrange um método para os automatizar implementações do software ao utilizar a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A extensão de painel de acesso também está disponível para o [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), nenhuma que requerem permissões de administrador para instalar.

##<a name="prerequisites"></a>Pré-requisitos

- Configurou [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e aderiu máquinas dos seus utilizadores para o seu domínio.
- Tem de ter a permissão de "Editar definições" para poder editar objectos de política de grupo (GPO). Por predefinição, os membros dos grupos de segurança seguintes têm esta permissão: os administradores de domínio, os administradores da empresa e proprietários do criador de política de grupo. [Saiba mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

##<a name="step-1-create-the-distribution-point"></a>Passo 1: Criar o ponto de distribuição

Em primeiro lugar, tem de colocar o pacote de instalação numa localização de rede que pode ser acedida de todas as máquinas que pretender instalar remotamente a extensão em. Para fazer isto, siga estes passos:

1. Iniciar sessão servidor como administrador

2. Na janela do **Gestor de servidor** , aceda aos **ficheiros e serviços de armazenamento**.

    ![Abrir ficheiros e serviços de armazenamento](./media/active-directory-saas-ie-group-policy/files-services.png)

3. Aceda ao separador **partilhas** . Em seguida, clique em **tarefas** > **Novo partilhar...**

    ![Abrir ficheiros e serviços de armazenamento](./media/active-directory-saas-ie-group-policy/shares.png)

4. Concluir o **Assistente de partilha de novo** e definir permissões para se certificar de que podem ser acedida a partir do máquinas dos seus utilizadores. [Saiba mais sobre partilhas.](https://technet.microsoft.com/library/cc753175.aspx)

5. Transferir o pacote do Microsoft Windows Installer seguinte (ficheiro. msi): [Extension.msi do painel de acesso](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access Panel Extension.msi)

6. Copie o pacote de instalação para uma localização pretendida na partilha.

    ![Copiar o ficheiro. msi está a partilhar.](./media/active-directory-saas-ie-group-policy/copy-package.png)

8. Certifique-se de que os computadores cliente são possível aceder ao pacote de instalação a partir da partilha. 

##<a name="step-2-create-the-group-policy-object"></a>Passo 2: Criar a política de grupo

1. Inicie sessão no servidor que aloja a sua instalação do Active Directory Domain Services (AD DS).

2. No Gestor de servidor, aceda a **Ferramentas** > **Gestão da política de grupo**.

    ![Aceda a ferramentas > cmdlet de política de grupo](./media/active-directory-saas-ie-group-policy/tools-gpm.png)

3. No painel esquerdo da janela de **Gestão de políticas de grupo** , veja a hierarquia de unidade organizacional (OU) e determinar quais âmbito que pretende aplicar a política de grupo. Por exemplo, pode decidir escolher um pequeno or para implementar alguns utilizadores para testar a ligação ou que pode escolher um nível superior or para implementar a toda a organização.

    > [AZURE.NOTE] Se pretender para criar ou editar a sua organização UOs (unidades), mudar novamente para o Gestor de servidor e aceda a **Ferramentas** > **utilizadores do Active Directory e computadores**.

4. Assim que tiver selecionado uma or, o botão direito do rato no mesmo e selecione **criar um GPO neste domínio e ligá-lo aqui...**

    ![Criar um novo GPO](./media/active-directory-saas-ie-group-policy/create-gpo.png)

5. Na linha de comandos **Novo GPO** , escreva um nome para o novo objecto de política de grupo.

    ![Atribuir um nome novo GPO](./media/active-directory-saas-ie-group-policy/name-gpo.png)

6. Botão direito do rato no objeto de política de grupo que acabou de criar e selecione **Editar**.

    ![Editar o novo GPO](./media/active-directory-saas-ie-group-policy/edit-gpo.png)

##<a name="step-3-assign-the-installation-package"></a>Passo 3: Atribuir o pacote de instalação

1. Determine se pretender para implementar a extensão com base na **Configuração do computador** ou de **Configuração do utilizador**. Ao utilizar a [configuração do computador](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a extensão será instalada no computador, independentemente de utilizadores que iniciam sessão-lo. Por outro lado, com a [configuração do utilizador](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), os utilizadores terão a extensão instalada para-los independentemente quais os computadores em que iniciam sessão.

2. No painel esquerdo da janela do **Editor de gestão de política de grupo** , aceda a um dos seguintes caminhos de pasta, dependendo do tipo de configuração que escolheu:
    - `Computer Configuration/Policies/Software Settings/`
    - `User Configuration/Policies/Software Settings/`

3. Com o botão direito numa **instalação de Software**, em seguida, selecione **Novo** > **pacote...**

    ![Criar um novo pacote de instalação de software](./media/active-directory-saas-ie-group-policy/new-package.png)

4. Vá para a pasta partilhada que contém o pacote de instalação a partir do [passo 1: criar o ponto de distribuição](#step-1-create-the-distribution-point), selecione o ficheiro. msi e clique em **Abrir**.

    > [AZURE.IMPORTANT] Se a opção partilhar estiver localizada no mesmo servidor, certifique-se de que está a aceder ao. msi através do caminho do ficheiro de rede, em vez do caminho do ficheiro local.

    ![Selecione o pacote de instalação a partir da pasta partilhada.](./media/active-directory-saas-ie-group-policy/select-package.png)

5. Na linha de comandos **Implementar Software** , selecione **atribuído** para o seu método de implementação. Em seguida, clique em **OK**.

    ![Selecione atribuído, em seguida, clique em OK.](./media/active-directory-saas-ie-group-policy/deployment-method.png)

A extensão agora for implementada a or que selecionou. [Saiba mais sobre a instalação de Software de política de grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

##<a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Passo 4: Automática-activar a extensão para o Internet Explorer 

Para além de executar o programa de instalação, todas as extensões para o Internet Explorer tem de estar explicitamente ativada antes de poder ser utilizada. Siga os passos abaixo para ativar a extensão de painel de acesso a utilização da política de grupo:

1. Na janela do **Editor de gestão de política de grupo** , aceda a um dos seguintes caminhos, dependendo do tipo de configuração que escolheu no [passo 3: atribuir o pacote de instalação](#step-3-assign-the-installation-package):
    - `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

2. Botão direito do rato na **Lista de suplementos**e selecione **Editar**.
    ![Edite lista de suplementos.](./media/active-directory-saas-ie-group-policy/edit-add-on-list.png)

3. Na janela da **Lista de suplementos** , selecione **ativado**. Em seguida, na secção **Opções** , clique em **Mostrar...**.

    ![Clique em ativar e, em seguida, clique em Mostrar...](./media/active-directory-saas-ie-group-policy/edit-add-on-list-window.png)

4. Na janela de **Mostrar conteúdo** , execute os seguintes passos:

    1. Para a primeira coluna (o campo **Nome do valor** ), copie e cole o ID de classe seguintes:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`

    2. Para a segunda coluna (campo de **valor** ), escreva o seguinte valor:`1`

    3. Clique em **OK** para fechar a janela de **Mostrar conteúdo** .

    ![Preencha os valores tal como especificado acima.](./media/active-directory-saas-ie-group-policy/show-contents.png)

5. Clique em **OK** para aplicar as alterações e feche a janela de **Lista de suplementos** .

A extensão deverá agora estar ativada nas máquinas no OU selecionada. [Saiba mais sobre como utilizar a política de grupo para ativar ou desativar suplementos do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

##<a name="step-5-optional-disable-remember-password-prompt"></a>Passo 5 (opcional): Desactivar pedido "Memorizar palavra-passe"

Quando os utilizadores iniciar sessão para Web sites que utilizam a extensão de painel de acesso, o Internet Explorer pode mostrar o pedido seguinte perguntar "Deseja armazenar a sua palavra-passe?"

![](./media/active-directory-saas-ie-group-policy/remember-password-prompt.png)

Se pretender impedir que os utilizadores vejam neste pedido, em seguida, siga os passos abaixo para impedir a conclusão automática de lembrar-se as palavras-passe:

1. Na janela do **Editor de gestão de política de grupo** , vá para o caminho listado abaixo. Tenha em atenção que esta definição de configuração só está disponível em **Configuração do utilizador**.
    - `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`

2. Localize a definição denominada **Ative a funcionalidade Conclusão automática para nomes de utilizador e palavras-passe em formulários**.

    > [AZURE.NOTE] Versões anteriores do Active Directory poderão listar esta definição com o nome **não permitir a conclusão automática para guardar as palavras-passe**. A configuração para essa definição difere da definição de descritos neste tutorial.

    ![Lembre-se procure isto em definições de utilizador.](./media/active-directory-saas-ie-group-policy/disable-auto-complete.png)

3. Clique com o botão direito do rato sobre a definição de acima e, selecione **Editar**.

4. Na janela do intitulado **Ative a funcionalidade Conclusão automática para nomes de utilizador e palavras-passe em formulários**, selecione **desativado**.

    ![Selecione desativar](./media/active-directory-saas-ie-group-policy/disable-passwords.png)

5. Clique em **OK** para aplicar estas alterações e feche a janela.

Os utilizadores já não poderão armazenar as suas credenciais ou utilizar a conclusão automática para aceder a credenciais armazenadas anteriormente. No entanto, esta política permite aos utilizadores continuar a utilizar a conclusão automática para outros tipos de campos do formulário, como os campos de pesquisa.

> [AZURE.WARNING] Se esta política estiver ativada depois dos utilizadores tem escolhido para armazenar alguns credenciais, isto irá política *não* desmarcar as credenciais que já tenham sido armazenadas.

##<a name="step-6-testing-the-deployment"></a>Passo 6: Testar a implementação

Siga os passos abaixo para verificar se a implementação de extensão foi bem sucedida:

1. Se implementado utilizando a **Configuração do computador**, inicie sessão no computador cliente que pertence a or que selecionou no [passo 2: criar a política de grupo](#step-2-create-the-group-policy-object). Se implementado utilizando a **Configuração do utilizador**, certifique-se iniciar sessão como um utilizador que pertence a esse or.

2. Poderá demorar algumas sinal ins a política de grupo muda para totalmente atualizar nesta máquina. Para forçar a atualização, abra uma janela de **linha de comandos** e execute o seguinte comando:`gpupdate /force`

3. Terá de reiniciar o computador para a instalação seja executada. Arranque poderá demorar significativamente mais tempo do que instala habitual enquanto a extensão.

4. Após reiniciar, abra o **Internet Explorer**. No canto superior direito da janela, clique em **Ferramentas de** (o ícone de engrenagem) e, em seguida, selecione **Gerir suplementos**.

    ![Aceda a ferramentas > Gerir suplementos](./media/active-directory-saas-ie-group-policy/manage-add-ons.png)

5. Na janela de **Gerir suplementos** , certifique-se de que tenha sido instalado a **Extensão do painel de acesso** e que o respetivo **Estado** tiver sido definida para **ativado**.

    ![Certifique-se de que a extensão de painel de acesso é instalada e ativada.](./media/active-directory-saas-ie-group-policy/verify-install.png)

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Acesso a aplicação e único início de sessão com o Azure Active Directory](active-directory-appssoaccess-whatis.md)
- [A extensão de painel de acesso de resolução de problemas para o Internet Explorer](active-directory-saas-ie-troubleshooting.md)
