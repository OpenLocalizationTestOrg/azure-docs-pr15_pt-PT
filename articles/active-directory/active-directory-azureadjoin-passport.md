<properties
    pageTitle="Autenticar identidades sem palavras-passe através do Microsoft Passport | Microsoft Azure"
    description="Fornece uma descrição geral do Microsoft Passport e informações adicionais sobre como implementar o Microsoft Passport."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Autenticação identidades sem palavras-passe através do Microsoft Passport

Os métodos de autenticação com palavras-passe por si só atuais não forem suficientes para impedir que os utilizadores seguros. Os utilizadores reutilizar e se esqueça de palavras-passe. Palavras-passe são breachable, phishable, passível de fendas e guessable. Também obter difícil memorizar e sujeito a ataques como "[passar o hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-microsoft-passport"></a>Sobre o Microsoft Passport
Microsoft Passport é uma chave de público/privado ou uma abordagem de autenticação baseada em certificado para organizações e consumidores direcionada para além das palavras-passe. Este formulário de autenticação depende de credenciais de par de pontos chave que podem substituir as palavras-passe e são resistentes a violações, thefts e phishing.

 Microsoft Passport permite que um utilizador autenticar para uma conta Microsoft, uma conta do Active Directory do Windows Server, uma conta do Microsoft Azure Active Directory (Azure AD) ou um serviço que não sejam Microsoft que suporta a autenticação da mesma rapidez com identidade Online (FIDO). Após uma verificação de dois passos inicial durante a inscrição para o Microsoft Passport, Microsoft Passport está configurado no dispositivo do utilizador e o utilizador define um gesto, que pode ser Olá do Windows ou um PIN. O utilizador fornece o gesto para verificar a identidade. Windows, em seguida, utiliza o Microsoft Passport para autenticar o utilizador e ajudá-los para aceder a serviços e recursos protegidos.

A chave privada é tornada disponível apenas através de um "gesto de utilizador" como um PIN, biometria ou um dispositivo remoto como um smart card que o utilizador utiliza para iniciar sessão no dispositivo. Esta informação estiver ligada a um certificado ou um par de chaves dissimétrica. A chave privada é hardware confirmada se o dispositivo tiver um chip fidedignos Platform módulo (TPM). A chave privada nunca sai do dispositivo.

A chave pública está registada com o Azure Active Directory e Windows Server Active Directory (no local). Fornecedores de identidade (IDPs) validam o utilizador mapeando a chave pública do utilizador para a chave privada e fornecem informações de início de sessão através de um tempo de palavra-passe (OTP), PhoneFactor ou um mecanismo de notificação diferente.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>Por que motivo empresas devem adotar o Microsoft Passport

Ao ativar o Microsoft Passport, empresas podem tornar os seus recursos de ainda mais seguras por:

* Configurar o Microsoft Passport com uma opção preferido hardware. Isto significa que serão geradas chaves no TPM 1.2 ou TPM 2.0 quando se encontra disponível. Quando TPM não estiver disponível, o software irá gerar a chave.

* Definir a complexidade e comprimento do PIN, e se a utilização de Olá está ativada na sua organização.

* Configurar o Microsoft Passport para suportar o cartão inteligente gosto cenários utilizando fidedignidade baseada em certificados.

## <a name="how-microsoft-passport-works"></a>Como funciona o Microsoft Passport
1. São geradas chaves o hardware por software ou TPM. Vários dispositivos de ter um chip TPM incorporado que proteja o hardware ao integrar chaves de criptografia dispositivos. TPM 1.2 ou TPM 2.0 gera chaves ou certificados que são criados a partir das teclas geradas.

2. O TPM certifica estas teclas de hardware vinculadas.

3. Um gesto de desbloquear única desbloqueia o dispositivo. Este gesto permite o acesso a vários recursos se o dispositivo está associado ao domínio ou Azure AD-associadas.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Como funciona o ciclo de vida do Microsoft Passport

![Ciclo de vida do Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

O diagrama anterior ilustra o par de chaves de público/privado e a validação pelo fornecedor de identidade. Cada um destes passos é explicado em detalhe aqui:

1. O utilizador prova a sua identidade através de vários métodos de verificação linguísticas incorporados (gestos, cartões inteligentes físicas, autenticação multifator) e envia estas informações para um fornecedor de identidade (IDP) como o Azure Active Directory ou no local do Active Directory.

2. O dispositivo, em seguida, cria a chave de, certifica a tecla, leva-o até a parte desta chave pública, anexa-com declarações de estação, a iniciar sessão e envia-o para IDP para registar a chave de.

4. Assim que a IDP regista a parte da chave pública, o IDP pede o dispositivo para iniciar sessão com a parte da chave privada.

5. Em seguida, o IDP valida e emite o token de autenticação que permite ao utilizador e o acesso do dispositivo aos recursos protegidos. IDPs pode escrever em diferentes plataformas aplicações ou utilize o suporte do browser (através do API JavaScript/Webcrypto) para criar e utilizar credenciais do Microsoft Passport para os seus utilizadores.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Os requisitos de implementação do Microsoft Passport
### <a name="at-the-enterprise-level"></a>Ao nível da empresa

* A empresa tem uma subscrição do Azure.

### <a name="at-the-user-level"></a>Ao nível de utilizador

* Computador do utilizador executa o Windows 10 Professional ou Enterprise.

Para obter instruções detalhadas implementação, consulte o artigo [Ativar o Microsoft Passport para trabalhar na organização](active-directory-azureadjoin-passport-deployment.md).


## <a name="additional-information"></a>Informações adicionais

* [Windows 10 para a empresa: formas de utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Expandir capacidades de nuvem para dispositivos Windows 10 através do Azure Active Directory participar](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de utilização para participar do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Ligar dispositivos façam parte de um domínio ao Azure AD para Windows 10 experiências](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar o participar do Azure AD](active-directory-azureadjoin-setup.md)
