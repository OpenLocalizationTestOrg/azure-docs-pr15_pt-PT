<properties
    pageTitle="Activar o Microsoft Windows Olá para empresas na sua organização | Microsoft Azure"
    description="Instruções de implementação para activar o Microsoft Passport na sua organização."
    services="active-directory"
    documentationCenter=""
    keywords="configurar o Microsoft Passport, Microsoft Windows Olá para implementação de empresas"
    authors="markusvi"
    manager="femila"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="markvi"/>


# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Activar o Microsoft Windows Olá para empresas na sua organização

Depois de [ligar dispositivos façam parte de um domínio do Windows 10 com o Azure Active Directory](active-directory-azureadjoin-devices-group-policy.md), faça o seguinte para activar o Microsoft Windows Olá para empresas na sua organização:

1. Implementar o Gestor de configuração do Centro de sistema  

2. Configurar definições de política

3. Configurar o perfil de certificado  




## <a name="deploy-system-center-configuration-manager"></a>Implementar o Gestor de configuração do Centro de sistema 

Para implementar os certificados de utilizador com base em Olá do Windows para chaves de negócio, precisa do seguinte:

- **Ramo de Gestor de configuração do sistema Centro actual** - tem de instalar a versão 1606 ou melhor. Para mais informações, consulte a [documentação para o Gestor de configuração do Centro de sistema](https://technet.microsoft.com/library/mt346023.aspx) e o [Centro de sistema Gestor de configuração do blogue de equipa](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
- **Infraestrutura de chave pública (PKI)** - para activar o Microsoft Windows Olá para empresas, utilizando os certificados de utilizador, tem de ter uma PKI no local. Se não tiver um ou que não pretende utilizá-la para certificados de utilizador, pode implementar um novo controlador de domínio que tem o Windows Server 2016 compilação 10551 (ou melhor) instalado. Siga os passos para [instalar um controlador de domínio réplica num domínio existente](https://technet.microsoft.com/library/jj574134.aspx) ou para [instalar uma nova floresta do Active Directory, se estiver a criar um novo ambiente](https://technet.microsoft.com/library/jj574166). (Os ISOs estão disponíveis para transferência no [Exchange de multimédia Signiant](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)


## <a name="configure-policy-settings"></a>Configurar definições de política

Para configurar o Microsoft Windows Olá para definições de política de negócio, tem duas opções:

- Política de grupo no Active Directory 
- O Gestor de configuração do Centro de sistema 


Política de grupo de utilizar no Active Directory é o método recomendado para configurar o Microsoft Windows Olá para as definições de política de empresas. 



Utilizar o Gestor de configuração do sistema Centro é o método preferencial quando a utiliza também para implementar certificados. Este cenário:

- Assegura a compatibilidade com os cenários de implementação mais recentes
- Necessita do lado do cliente 1607 de versão do Windows 10 ou melhor.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Configurar o Microsoft Windows Olá para empresas através da política de grupo no Active Directory

 
**Passos**:

1.  Abra o Gestor de servidor e navegue para **Ferramentas** > **Gestão da política de grupo**.
2.  A partir de gestão da política de grupo, navegue para o nó do domínio que corresponde ao domínio na qual pretende ativar o Azure AD participar.
3.  **Objectos de política de grupo**com o botão direito e selecione **Novo**. Objecto de política de grupo dê um nome ao, por exemplo, ativar Windows Olá para empresas. Clique em **OK**.
4.  O novo objecto de política de grupo com o botão direito e, em seguida, selecione **Editar**.
5.  Navegue para a **configuração do computador** > **políticas** > **Modelos administrativos** > **componentes do Windows** > **Olá do Windows para empresas**.
6.  Botão direito do rato **Ativar Windows Olá para empresas**e, em seguida, selecione **Editar**.
7.  Selecione o botão de opção **ativado** e, em seguida, clique em **Aplicar**. Clique em **OK**.
8.  Agora pode ligar a política de grupo para uma localização da sua escolha. Para ativar esta política para todos os dispositivos Windows 10 façam parte de um domínio na sua organização, ligação a política de grupo para o domínio. Por exemplo:
 - Uma unidade organizacional específica (OU) no Active Directory onde estarão localizados computadores façam parte de um domínio do Windows 10
 - Um grupo de segurança específica que contém o Windows 10 computadores associados ao domínio que serão automaticamente registados com Azure AD


### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>Configurar o Windows Olá para empresas utilizando o Gestor de configuração do Centro de sistema


**Passos**:


1. Abrir o **Gestor de configuração do Centro de sistema**e, em seguida, navegue para **activos e conformidade > definições de conformidade > acesso a recursos da empresa > Windows Olá empresas perfis de**.

    ![Configurar o Windows Olá para empresas](./media/active-directory-azureadjoin-passport-deployment/01.png)


2. Na barra de ferramentas no canto superior, clique em **Criar Windows Olá para empresas perfil**.

    ![Configurar o Windows Olá para empresas](./media/active-directory-azureadjoin-passport-deployment/02.png)

2. Na caixa de diálogo **Geral** , efetue os seguintes passos:

    ![Configurar o Windows Olá para empresas](./media/active-directory-azureadjoin-passport-deployment/03.png)

    um. Na caixa de texto **nome** , escreva um nome para o seu perfil, por exemplo, **O meu perfil WHfB**.

    b. Clique em **seguinte**.


2. Na caixa de diálogo **Plataformas suportadas** , selecione as plataformas que serão aprovisionadas com este Windows Olá de perfil de negócio e, em seguida, clique em **seguinte**.

    ![Configurar o Windows Olá para empresas](./media/active-directory-azureadjoin-passport-deployment/04.png)


2. Na caixa de diálogo **Definições** , execute os seguintes passos:

    ![Configurar o Windows Olá para empresas](./media/active-directory-azureadjoin-passport-deployment/05.png)

    um. Como **Configurar o Windows Olá para empresas**, selecione **ativado**.

    b. Como **utilizar um módulo de plataforma fidedignos (TPM)**, selecione **necessário**. 

    c. **Método de autenticação**, selecione **baseada em certificados**.

    d. Clique em **seguinte**.



2. Na caixa de diálogo **Resumo** , clique em **seguinte**.

2. Na caixa de diálogo **conclusão** , clique em **Fechar**.


2. Na barra de ferramentas no canto superior, clique em **Implementar**.

    ![Configurar o Windows Olá para empresas](./media/active-directory-azureadjoin-passport-deployment/06.png)



## <a name="configure-the-certificate-profile"></a>Configurar o perfil de certificado 

Se estiver a utilizar uma autenticação baseada em certificado para autenticação no local, terá de configurar e implementar um perfil de certificado. Esta tarefa requer que configurar um servidor NDES e a função de ponto de registo de certificado de site no Gestor de configuração de centro de sistema. Para obter mais detalhes, consulte os [Pré-requisitos de perfis de certificado no Gestor de configuração de](https://technet.microsoft.com/library/dn261205.aspx).

1. Abrir o **Gestor de configuração do Centro de sistema**e, em seguida, navegue para **activos e conformidade > definições de conformidade > acesso a recursos da empresa > perfis de certificado**.


2. Selecione um modelo que tem o cartão inteligente início de sessão no utilização alargada da chave (EKU).

Na página de **Inscrição SCEP** do perfil de certificado, tem de escolher **falhar instalar para Passport para outra forma de trabalho** como o **Fornecedor de armazenamento de chave**.



## <a name="next-steps"></a>Próximos passos
* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Autenticação identidades sem palavras-passe através do Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)
