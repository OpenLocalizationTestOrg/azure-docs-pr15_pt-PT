<properties
    pageTitle="Vulnerabilidades detectadas pelo Azure Active Directory identidade Protection | Microsoft Azure"
    description="Descrição geral das vulnerabilidades detectado pelo protecção de identidade do Azure Active Directory."
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, deteção de aplicação de nuvem, gerir aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detectadas pelo protecção de identidade do Azure Active Directory 

Vulnerabilidades são fraquezas no seu ambiente que pode tirar partido da intruso. Recomendamos que resolva este vulnerabilidade para melhorar a postura de segurança da sua organização e impedir que os intrusos explore-los.
<br><br>
![vulnerabilidades](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

As secções seguintes fornecem-lhe uma descrição geral das vulnerabilidades comunicado por proteção de identidade.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registo de autenticação multifator não configurado 

Esta vulnerabilidade ajuda a controlar a implementação do Azure a autenticação Multifator na sua organização. 

Autenticação multifator Azure fornece uma segunda camada de segurança para autenticação de utilizador. -Ajuda-o a salvaguarda o acesso aos dados e aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião. Fornece-forte autenticação através de um intervalo de opções de verificação fácil — chamada telefónica, mensagem de texto ou aplicação móvel notificação ou verificação de código e 3 festa JURAMENTO tokens.

Recomendamos que exigir autenticação Multifator de Azure para suplementos de início de sessão do utilizador. Autenticação multifator reprodução de uma função de chave no políticas de acesso condicional com base em risco disponíveis através de proteção de identidade.

Para obter mais detalhes, consulte o artigo [o que é o Azure a autenticação Multifator?](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>Aplicações de que não é gerido na nuvem

Esta vulnerabilidade ajuda-o a identificar aplicações na nuvem que não é gerido na sua organização.
 
Empresas modernas, os departamentos de TI são geralmente noção de todas as aplicações de nuvem que os utilizadores na sua organização estiver a utilizar para fazer o seu trabalho. É fácil ver porque é que os administradores teria preocupações em acesso não autorizado aos dados empresariais, perdas de dados possíveis e outros riscos de segurança. 

Recomendamos que a sua organização implementar nuvem deteção de aplicação para descobrir as aplicações que não é gerido na nuvem e para gerir estas aplicações utilizando o Azure Active Directory.

Para obter mais detalhes, consulte o artigo [localizar aplicações que não é gerido nuvem com a deteção de aplicação na nuvem](active-directory-cloudappdiscovery-whatis.md).



##<a name="security-alerts-from-privileged-identity-management"></a>Alertas de segurança de gestão de identidades privilegiados

Esta vulnerabilidade ajuda-o a descobrir e resolver alertas sobre identidades privilegiadas na sua organização.  

Para permitir que os utilizadores a efectuar operações privilegiadas, organizações tem de conceder acesso privilegiados utilizadores temporária ou permanente no Azure AD, recursos Azure ou do Office 365 ou outras aplicações SaaS. Cada um destes utilizadores privilegiados aumenta a superfície de ataque da sua organização. Esta vulnerabilidade ajuda-na identificar os utilizadores com acesso privilegiado desnecessário e tomar medidas adequadas para reduzir ou eliminar o risco que constituem. 

Recomendamos que a sua organização utiliza o Azure AD privilegiados gestão de identidades para gerir, controlo, e monitor privilégios identidades e o seu acesso aos recursos no Azure AD, bem como outros serviços onlinehttps da Microsoft como o Office 365 ou o Microsoft Intune.

Para obter mais detalhes, consulte o artigo [da gestão de identidades do Azure AD privilegiados](active-directory-privileged-identity-management-configure.md). 



## <a name="see-also"></a>Consulte também

 - [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)
