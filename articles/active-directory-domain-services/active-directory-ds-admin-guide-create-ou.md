<properties
    pageTitle="Azure Active Directory Domain Services: Guia de administração | Microsoft Azure"
    description="Criar uma unidade organizacional (OU) em domínios de serviços de domínio do Azure AD geridos"
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
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Crie uma unidade organizacional (OU) um domínio gerido de serviços de domínio do Azure AD
Azure domínios geridos de serviços de domínio do AD incluem dois contentores incorporados denominados 'AADDC computadores' e 'AADDC utilizadores' respetivamente. O contentor 'AADDC computadores' tem objetos de computador para todos os computadores que são associados ao domínio gerido. O contentor 'AADDC utilizadores' inclui utilizadores e grupos no inquilino Azure AD. Por vezes, poderá ser necessário criar contas de serviço no domínio gerido para implementar das cargas de trabalho. Para o efeito, pode criar uma unidade organizacional personalizado (OU) no domínio gerido e criar contas de serviço dentro desse or. Este artigo mostra-lhe como criar um or no seu domínio gerido.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Instalar ferramentas de administração AD num computador virtual façam parte de um domínio para administração remota
Azure domínios geridos de serviços de domínio do AD podem ser geridos remotamente utilizando familiares do Active Directory as ferramentas administrativas como o Active Directory administrativo centro (ADAC) ou AD PowerShell. Os administradores de inquilino não possui privilégios para ligar a controladores de domínio no domínio gerido através do ambiente de trabalho remoto. Para administrar o domínio gerido, instale a funcionalidade de ferramentas de administração do AD numa máquina virtual aderida ao domínio gerido. Consulte o artigo intitulado [administrar um domínio geridos de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md) para obter instruções.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Criar uma unidade organizacional no domínio gerido
Agora que as ferramentas administrativas do AD instaladas no domínio associadas máquina virtual, pode Utilizamos estas ferramentas para criar uma unidade de organização no domínio gerido. Execute os seguintes passos:

> [AZURE.NOTE] Apenas os membros do grupo 'AAD CC administradores' tem os privilégios necessários para criar um or personalizado. Certifique-se de que execute os seguintes passos como um utilizador que pertence a este grupo.

1. A partir do ecrã Iniciar, clique em **Ferramentas administrativas**. Deverá ver as ferramentas administrativas do AD instaladas no computador virtual.

    ![Ferramentas administrativas instaladas no servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Clique em **Centro de administração do Active Directory**.

    ![Centro de administração do Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Para ver o domínio, clique no nome de domínio no painel à esquerda (por exemplo, ' contoso100.com').

    ![ADAC - domínio de vista](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. No painel de **tarefas** do lado direito, clique em **Novo** sob o nó do nome de domínio. Neste exemplo, vamos clique em **Novo** sob o nó 'contoso100(local)' no painel de **tarefas** do lado direito.

    ![ADAC - or novo](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Deverá ver a opção para criar uma unidade organizacional. Clique em **Unidade organizacional** para iniciar a caixa de diálogo **Criar unidade organizacional** .

6. Na caixa de diálogo **Criar unidade organizacional** , especifique um **nome** para a nova OU. Forneça uma descrição breve para o OR. Também pode definir o campo **Gerido por** para o OR. Para criar o or personalizado, clique em **OK**.

    ![ADAC - or caixa de diálogo Criar](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. O or recentemente criado deverá agora aparecer no Centro de administração AD (ADAC).

    ![ADAC - or criado](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>/ Segurança das permissões para recentemente criado ou
Por predefinição, o utilizador (membro do grupo 'AAD CC administradores') que criou a or personalizado é concedido privilégios administrativos (controlo total) sobre o OR. O utilizador, em seguida, pode prosseguir e conceder privilégios aos outros utilizadores ou ao grupo de 'AAD CC administradores' conforme pretender. Conforme visto na captura de ecrã seguinte, o utilizador 'bob@domainservicespreview.onmicrosoft.com' que criou a nova unidade organizacional 'MyCustomOU' é concedida controlo total sobre a mesma.

 ![ADAC - segurança or novas](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Notas sobre como administrar ou personalizado
Agora que criou uma or personalizado, pode prosseguir e criar utilizadores, grupos, computadores e contas de serviço neste or. Não pode mover utilizadores ou grupos dos 'AAD CC utilizadores' or para ou personalizada.

> [AZURE.WARNING] Contas de utilizador, grupos, contas de serviço e objetos de computador que criar em personalizado ou não estão disponíveis no seu inquilino do Azure AD. Por outras palavras, estes objetos que não apresentam para cima de utilizar a API do Azure AD Graph ou na IU do Azure AD. Estes objectos só estão disponíveis no seu domínio gerido de serviços de domínio do Azure AD.


## <a name="related-content"></a>Conteúdo relacionado

- [Administrar um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)

- [Centro de administração do Active Directory: Introdução](https://technet.microsoft.com/library/dd560651.aspx)

- [Guia passo a passo de contas de serviço](https://technet.microsoft.com/library/dd548356.aspx)
