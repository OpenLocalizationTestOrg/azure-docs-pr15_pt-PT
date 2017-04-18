<properties
    pageTitle="Definir a política de acesso condicional com base no dispositivo ligado Azure Active Directory aplicações | Microsoft Azure"
    description="Definir as políticas de acesso condicional com base no dispositivo para aplicações do Azure AD-ligado."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="markvi"/>


# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Definir a política de acesso condicional com base no dispositivo para aplicações ligado Azure Active Directory


Azure Active Directory (Azure AD) com base no dispositivo acesso condicional pode ajudar a proteger recursos a partir da organização:

- Tenta Access a partir de dispositivos geridos ou desconhecidos.
- Dispositivos que não cumprem as políticas de segurança da sua organização.

Para obter uma descrição geral do access condicional, consulte o artigo [acesso condicional do Azure Active Directory](active-directory-conditional-access.md).

Pode definir acesso condicional com base no dispositivo as políticas para proteger estas aplicações:

- Office 365 SharePoint Online, para proteger a sua organização sites e documentos
- Office 365 Exchange Online, para proteger o e-mail da sua organização
- Software como um aplicações de serviço (SaaS) que estão ligadas à Azure AD para autenticação
- No local aplicações que são publicadas, utilizando os serviços de Proxy de aplicação do Azure AD

Para definir uma política de acesso condicional com base no dispositivo, no portal do Azure, vá para a aplicação específica no diretório.


  ![Lista de aplicações no diretório portal Azure] (./media/active-directory-conditional-access-policy-connected-applications/01.png "Aplicações")


Selecione a aplicação e, em seguida, clique no separador **Configurar** para definir a política de acesso condicional.  


  ![Configurar a aplicação] (./media/active-directory-conditional-access-policy-connected-applications/02.png "Dispositivo com base regras de acesso")




Para definir uma política de acesso condicional com base no dispositivo, na secção **dispositivo com base regras de acesso** , **Ativar as regras de acesso**, selecione **sobre**.

Uma política de acesso condicional com base no dispositivo tem três componentes:

- **Aplicar a**. O âmbito de utilizadores que a política aplica-se ao.

- **Regras de dispositivo**. As condições um dispositivo tem de cumprir antes de que podem aceder à aplicação.

- **Imposição de aplicação**. As aplicações de cliente (nativo versus browser) a política se aplica ao.

  ![Os três componentes de uma política de acesso com base no dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/03.png "Dispositivo com base regras de acesso")


## <a name="select-the-users-the-policy-applies-to"></a>Selecione os utilizadores que a política aplica-se ao

Na secção de **Aplicar a** , pode selecionar o âmbito de utilizadores que esta política aplica-se ao.

Tem duas opções quando cria um âmbito de política de acesso para os utilizadores:

- **Todos os utilizadores**. Aplica a política para todos os utilizadores acedam à aplicação.

- **Grupos**. Limite a política para utilizadores que se encontram um membro de um grupo específico.

![Política de aplicar a todos os utilizadores ou a um grupo] (./media/active-directory-conditional-access-policy-connected-applications/11.png "Aplicar a")


 Para excluir um utilizador a partir de uma política, selecione a caixa de verificação **exceto** . Isto é útil quando precisar de conceder permissões a um utilizador específico temporariamente aceder à aplicação. Selecione esta opção, por exemplo, se alguns dos seus utilizadores têm dispositivos que não sejam está preparados para acesso condicional. Os dispositivos poderão não ser registados ou estão prestes a ser terminar conformidade.


## <a name="select-the-conditions-that-devices-must-meet"></a>Selecione as condições que devem cumprir dispositivos

Utilizar **Regras de dispositivo** para definir as condições um dispositivo tem de cumprir para aceder à aplicação.

Pode definir acesso condicional com base no dispositivo para estes tipos de dispositivo:

- Actualização de aniversário do Windows 10, Windows 8.1 e Windows 7
- Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2
- dispositivos iOS (iPad, iPhone)
- Dispositivos Android

Suporte para Mac é brevemente.

  ![Aplicar política para dispositivos] (./media/active-directory-conditional-access-policy-connected-applications/04.png "Aplicações")

 >[AZURE.NOTE] Para obter informações sobre as diferenças entre façam parte de um domínio e Azure AD-associadas os dispositivos, consulte o artigo [dispositivos de utilizar o Windows 10, no seu local de trabalho](active-directory-azureadjoin-windows10-devices.md).

Tem duas opções para regras de dispositivos:

- **Todos os dispositivos tem de estar em conformidade**. Todas as plataformas de dispositivo acedam à aplicação tem de estar em conformidade. Dispositivos que executar plataformas que não suportam acesso condicional com base no dispositivo têm o acesso negados.

- **Apenas os dispositivos selecionados tem de estar em conformidade**. Apenas plataformas de dispositivos específicos tem de estar em conformidade. Outras plataformas ou noutras plataformas que podem aceder à aplicação, tem acesso.

  ![Definir o âmbito de regras de dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/05.png "Aplicações")

Azure dispositivos associarem ao AD são compatíveis se são marcadas como **em conformidade** no diretório por Intune ou por um sistema de gestão de dispositivos móveis terceiros integra-se com o Azure AD.

É compatível com um dispositivo façam parte de um domínio se:

- É registada com Azure AD. Muitas organizações tratam dispositivos associarem ao domínio como fidedignos dispositivos.
- Este é assinalado como **em conformidade** no Azure AD pelo sistema Centro de Gestor de configuração do 2016.

 ![Dispositivos façam parte de um domínio que são compatíveis com] (./media/active-directory-conditional-access-policy-connected-applications/06.png "Regras de dispositivo")

Dispositivos com Windows pessoais são compatíveis se são marcadas como **em conformidade** no diretório por Intune ou por um sistema de gestão de dispositivos móveis de terceiros que integra-se com o Azure AD.

Dispositivos Windows não são compatíveis se estão marcados como **em conformidade** no diretório por Intune.

 >[AZURE.NOTE] Para mais informações sobre como configurar o Azure AD para conformidade do dispositivo nos sistemas de gestão diferente, consulte o artigo [acesso condicional do Azure Active Directory](active-directory-conditional-access.md).


Pode selecionar um ou vários plataformas de dispositivos para uma política de acesso baseado no dispositivo. Isto inclui Android, iOS, Windows Mobile (Windows 8.1 telemóveis e tablets) e Windows (todos os outros dispositivos Windows, incluindo todos os dispositivos Windows 10).
Avaliação da política ocorre apenas plataformas selecionado. Se um dispositivo que tenta obter acesso não estiver a executar uma das plataformas selecionadas, o dispositivo pode aceder à aplicação se o utilizador tem acesso. É avaliada sem política de dispositivo.

![Selecione plataformas para regras de dispositivo] (./media/active-directory-conditional-access-policy-connected-applications/07.png "Regras de dispositivo")


## <a name="set-policy-evaluation-for-a-type-of-application"></a>Configurar a avaliação da política para um tipo de aplicação

Na secção **Imposição de aplicação** , defina o tipo de aplicações que irá avaliar a política de acesso de dispositivo ou utilizador.

Tem duas opções para o tipo de aplicação para incluir:

- Browser e aplicações nativas
- Apenas a aplicações nativa

![Escolher browser ou aplicações nativas] (./media/active-directory-conditional-access-policy-connected-applications/08.png "Aplicações")

Para impor a política de acesso para aplicações, selecione **para o browser e aplicações nativas**. Em seguida, pode incluir:

- Browsers (por exemplo, o Microsoft Edge no Windows 10) ou do Safari no iOS.
- Aplicações que utilizam o Active Directory autenticação biblioteca (ADAL) qualquer plataforma ou que utilize o WebAccountManager WAM () API no Windows 10.

>[AZURE.NOTE] Para mais informações sobre o suporte do browser e outras considerações para um utilizador que está a aceder a um dispositivo com base no, aplicação protegida autoridade do certificado, consulte o artigo [acesso condicional do Azure Active Directory](active-directory-conditional-access.md).

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Ajudar a proteger o acesso ao e-mail a partir de aplicações com base no Exchange ActiveSync

Nas aplicações do Office 365 Exchange Online, pode utilizar o Exchange ActiveSync para bloquear o acesso de correio eletrónico para as aplicações de e-mail baseada no Exchange ActiveSync.

![Opções de conformidade do Exchange ActiveSync] (./media/active-directory-conditional-access-policy-connected-applications/09.png "Aplicações")

![Exigir um dispositivo compatível com para aceder ao e-mail] (./media/active-directory-conditional-access-policy-connected-applications/10.png "Aplicações")


## <a name="next-steps"></a>Próximos passos

- [Acesso ao condicional Azure Active Directory](active-directory-conditional-access.md)
