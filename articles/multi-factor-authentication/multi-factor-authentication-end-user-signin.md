<properties
    pageTitle="Experiência de início de sessão MFA Azure com a autenticação Multifator do Azure"
    description="Esta página irá fornecer-lhe orientações sobre onde ir para ver os diferentes métodos de início de sessão disponíveis com o Azure MFA."
    keywords="autenticação de utilizador, início de sessão no experiência, iniciar sessão com o número de telemóvel, iniciar sessão com o telefone do escritório"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>Início de sessão experiência com o Azure a autenticação Multifator
> [AZURE.NOTE]  A seguinte documentação fornecida nesta página mostra uma experiência de início de sessão típica.  Para obter ajuda com a iniciar sessão, consulte [está a ter problemas com a autenticação Multifator do Azure](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>O que vai ser sua experiência de início de sessão?
Dependendo de como iniciar sessão e utilize a autenticação multifator, a sua experiência serão diferentes.  Nesta secção podemos irá fornecer informações sobre o que esperar quando iniciar sessão.  Escolha aquele que melhor descreve o que fazer:


O que são fazer?|Descrição
:------------- | :------------- |
[Iniciar sessão com o telemóvel ou o office mobile](#signing-in-with-mobile-or-office-phone) | Este é o que pode esperar de iniciar sessão através do seu telefone ou o office mobile.
[Iniciar sessão com a aplicação de Microsoft Authenticator com notificação](#signing-in-with-the-microsoft-authenticator-app-using-notification) | Este é o que pode esperar utilizando a aplicação Microsoft Authenticator com as notificações.
[Iniciar sessão com a aplicação Microsoft Authenticator utilizar código de verificação](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|Este é o que pode esperar utilizando o Microsoft Authenticator thapp com um código de verificação.
[Iniciar sessão com um método alternativo](#signing-in-with-an-alternate-method)|Isto irá mostrar o que esperar se pretende utilizar um método alternativo.

## <a name="signing-in-with-mobile-or-office-phone"></a>Iniciar sessão com o telemóvel ou o office mobile

As seguintes informações irão descrevem a experiência de utilização de autenticação multifator com o seu telemóvel ou o office mobile.

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>Para iniciar sessão com uma chamada para o office ou o número de telemóvel

- Inicie sessão na aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
- Microsoft irá efetuar a chamada é.

![Chamadas da Microsoft](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- Atender o telefone e clicar na tecla #.

![Resposta](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- Agora deve ser assinado.</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>Iniciar sessão com a aplicação de Microsoft Authenticator com notificação

As seguintes informações irão descrevem a experiência de utilização de autenticação multifator com a aplicação Microsoft Authenticator quando são enviadas uma notificação.

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>Para iniciar sessão com uma notificação enviada a aplicação Microsoft Authenticator

- Inicie sessão na aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
- Microsoft irá enviar uma notificação.

![Microsoft envia uma notificação](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- Atender o telefone e prima a tecla de verificar.  Se a sua empresa necessita de um PIN lhe-á ser pedido para o mesmo aqui.

![Verifique se](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![Programa de configuração](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- Agora deve ser assinado.


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>Iniciar sessão com a aplicação Microsoft Authenticator utilizar código de verificação

As seguintes informações irão descrevem a experiência de utilização de autenticação multifator com a aplicação Microsoft Authenticator quando está a utilizar com um código de verificação.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Para iniciar sessão utilizando um código de verificação com a aplicação Microsoft Authenticator

- Inicie sessão na aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
- Microsoft irá pedir-lhe um código de verificação.

![Introduza o código de verificação](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- Abra a aplicação Microsoft Authenticator no seu telemóvel e introduza o código na caixa onde está a iniciar sessão.

![Obter o código](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- Agora deve ser assinado.


## <a name="signing-in-with-an-alternate-method"></a>Iniciar sessão com um método alternativo


A seguinte secção mostrar-lhe como iniciar sessão com um método alternativo quando o seu método de primário poderão não estar disponível.

### <a name="to-sign-in-with-an-alternate-method"></a>Para iniciar sessão com um método alternativo

- Inicie sessão na aplicação ou serviço como o Office 365 com o seu nome de utilizador e palavra-passe.
- Selecione utilizar uma hierarquia.  Será apresentar com uma escolha opções diferentes. O número que vai ver terá basear quantos tiver o programa de configuração.

![Utilizar o método alternativo](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- Escolher um método alternativo e inicie sessão.
