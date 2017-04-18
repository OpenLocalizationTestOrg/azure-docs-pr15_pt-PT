<properties
    pageTitle="Resolução de problemas de acesso do Azure Active Directory | Microsoft Azure"
    description="Aprenda passos que pode tomar para resolver problemas de acesso com recursos online da sua organização."
    services="active-directory"
    keywords="dispositivo de acesso baseado em condicional, registo de dispositivo, ativar o registo de dispositivo, registo de dispositivo e a MDM"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="markvi"/>


# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Resolução de problemas de acesso do Azure Active Directory

Tentar aceder à intranet SharePoint Online da sua organização e, receberá uma mensagem de erro "acesso negado". O que deve fazer?

Este artigo abrange os passos de remediação que podem ajudar a resolver problemas de acesso com recursos online da sua organização.

Para ajudar a resolver o Azure Active Directory (Azure AD) acesso problemas, aceda à secção no artigo que abrange sua plataforma do dispositivo:

-   Dispositivo Windows
-   dispositivo iOS (verificar anterior mais cedo para obter assistência com ou iPhone e iPads.)
-   Dispositivo Android (que deve verificar mais cedo para obter assistência com Android telemóveis e tablets.)

## <a name="access-from-a-windows-device"></a>Acesso a partir de um dispositivo Windows

Se o seu dispositivo é executado um dos seguintes plataformas, veja nas secções seguintes para a mensagem de erro que é apresentado quando tenta aceder a uma aplicação ou serviço:

- Windows 10
- Windows 8.1
- Windows 8
- Windows 7
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

### <a name="device-is-not-registered"></a>Dispositivo não está registado

Se o seu dispositivo não estiver registado com Azure AD e a aplicação estiver protegida com uma política de dispositivo, poderá ver uma página que mostra uma destas mensagens de erro:

!["Não é possível aceder aí a partir daqui" mensagens para dispositivos não registados] (./media/active-directory-conditional-access-device-remediation/01.png "Cenário")

Se o seu dispositivo está associado ao domínio para o Active Directory na sua organização, tente o seguinte:

1.  Certifique-se de que inicie sessão Windows utilizando a sua conta do trabalho (sua conta do Active Directory).
2.  Ligar à rede empresarial através de uma rede privada virtual (VPN) ou DirectAccess.
3.  Depois de estar ligado, prima a tecla de logótipo do Windows + a tecla L para bloquear a sessão do Windows.
4.  Introduza as suas credenciais de conta do trabalho para desbloquear a sessão do Windows.
5.  Aguardar um minuto e, em seguida, tente novamente aceder a aplicação ou serviço.
6.  Se vir a mesma página, clique na ligação **mais detalhes** e, em seguida, contacte o administrador com os detalhes.

Se o seu dispositivo não estiver façam parte de um domínio e executa o Windows 10, tem duas opções:

- Executar a associação do Azure AD
- Adicionar a sua conta escolar ou profissional para Windows

Para informações sobre como são diferentes estas opções, consulte o artigo [utilizar o Windows 10 dispositivos no seu local de trabalho](active-directory-azureadjoin-windows10-devices.md).

Para executar o Azure AD participar, execute os passos seguintes para a plataforma de que dispositivo é executada no. (Azure AD associação não está disponível em telemóveis Windows Phone.)

**Atualização do Windows 10 aniversário**

1.  Abra a aplicação de **Definições** .
2.  Clique em **contas** > **trabalho do Access ou na escola**.
3.  Clique em **Ligar**.
4.  Clique em **participar neste dispositivo Azure AD**.
5.  Autenticar para a sua organização, forneça a autenticação multifator se pedida e, em seguida, siga os passos que são apresentados.
6.  Terminar sessão e, em seguida, inicie sessão com a sua conta do trabalho.
7.  Para aceder à aplicação, tente novamente.


**Atualização do Windows 10 de Novembro de 2015**

1.  Abra a aplicação de **Definições** .
2.  Clique em **sistema** > **sobre**.
3.  Clique em **aderir Azure AD**.
4.  Autenticar para a sua organização, forneça a autenticação multifator se pedida e, em seguida, siga os passos que são apresentados.
5.  Terminar sessão e, em seguida, inicie sessão com a sua conta do trabalho (sua conta do Azure AD).
6.  Para aceder à aplicação, tente novamente.

Para adicionar a sua conta escolar ou profissional, efetue os seguintes passos:

**Atualização do Windows 10 aniversário**

1.  Abra a aplicação de **Definições** .
2.  Clique em **contas** > **trabalho do Access ou na escola**.
3.  Clique em **Ligar**.
4.  Autenticar para a sua organização, forneça a autenticação multifator se pedida e, em seguida, siga os passos que são apresentados.
5.  Para aceder à aplicação, tente novamente.


**Atualização do Windows 10 de Novembro de 2015**

1.  Abra a aplicação de **Definições** .
2.  Clique em **contas** > **suas contas**.
3.  Clique em **adicione escolar ou profissional de conta**.
4.  Autenticar para a sua organização, forneça a autenticação multifator se pedida e, em seguida, siga os passos que são apresentados.
5.  Para aceder à aplicação, tente novamente.

Se o seu dispositivo não está associado ao domínio e executa o Windows 8.1, para fazer uma associação do local de trabalho e de se inscrever no Microsoft Intune, efetue os seguintes passos:

1.  Abra **definições do PC**.
2.  Clique em **rede** > **local de trabalho**.
3.  Clique em **participar**.
4.  Autenticar para a sua organização, forneça a autenticação multifator se pedida e, em seguida, siga os passos que são apresentados.
5.  Clique em **Ativar**.
6.  Para aceder à aplicação, tente novamente.


### <a name="browser-is-not-supported"></a>Browser não for suportado

Poderá ser negado acesso se está a tentar aceder a uma aplicação ou serviço, utilizando um dos seguintes browsers:

- Chrome, Firefox ou qualquer outro browser diferente Microsoft Edge ou Microsoft Internet Explorer no Windows 10 ou Windows Server 2016
- Firefox no Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2

Verá uma página de erro que tem este aspeto:

![Mensagem "Não é possível aceder aí a partir daqui" para browsers não suportadas] (./media/active-directory-conditional-access-device-remediation/02.png "Cenário")

A remediação apenas é utilizar um browser compatível com a aplicação para a sua plataforma do dispositivo.

## <a name="next-steps"></a>Próximos passos

[Acesso ao condicional Azure Active Directory](active-directory-conditional-access.md)
