<properties
    pageTitle="Híbrido identidade: Ferramentas de integração de diretórios comparação | Microsoft Azure"
    description="Esta é a página fornece uma tabela completa que compara as ferramentas de integração de diretórios vários que podem ser utilizadas para integração de diretórios."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação das ferramentas de integração de diretórios de identidade híbrido

Ao longo dos anos, as ferramentas de integração de diretórios tem produzidos e evoluiu um.  Este documento é para ajudar a fornecer uma vista consolidada destas ferramentas e uma comparação das funcionalidades que estão disponíveis em cada uma.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Ligação do Azure AD incorpora os componentes e a funcionalidade disponibilizadas anteriormente como Dirsync e AAD Sync. Estas ferramentas já não estão a ser lançadas individualmente e todas as melhorias futuras são incluídas em actualizações para a ligação do Azure AD, para que saiba sempre onde podem obter a funcionalidade mais recente.
>
>DirSync e Azure AD Sync são descontinuada. Podem encontrar mais informações no [aqui](active-directory-aadconnect-dirsync-deprecated.md).


Utilize a seguinte chave para cada uma das tabelas.

● = Disponível agora  
F = lançamento futuro  
PP = pré-visualização do público  

## <a name="on-premises-to-cloud-synchronization"></a>No local para o Cloud sincronização

| Funcionalidade  | Ligar o Azure Active Directory  | Serviços de sincronização do Azure Active Directory (AAD Sync) | Ferramenta de sincronização do Azure Active Directory (DirSync)| Gestor de identidades do Forefront 2010 R2 (FIM) |Gestor de identidades do Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Ligar a única no local floresta AD | ● | ● | ● | ● |● |
| Ligar a vários no local florestas AD |●  | ● |  | ● |● |
| Ligar a várias organizações de Exchange no local | ● |  |  |  | |
| Ligar a única diretório no local LDAP | F |  |  | ● |● |
| Ligar a vários directórios LDAP no local |F  |  |  | ● |● |
| Ligar a no local AD e no local directórios LDAP |F  |  |  | ● |● |
| Ligar a sistemas personalizados (por exemplo, SQL, Oracle, MySQL, etc.) | F |  |  | ● |● |
| Sincronizar os atributos de cliente definido (extensões de diretório) | ● |  |  |  |  |
|Ligar a HR no local (por exemplo, SAP, Oracle eComércio, PeopleSoft)| F |  |  | ● |● |
|Suporta conectores e regras de sincronização do FIM para aprovisionar para sistemas de no local.|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Nuvem para a sincronização no local

| Funcionalidade  | Ligar o Azure Active Directory  | Serviços de sincronização do Azure Active Directory | Ferramenta de sincronização do Azure Active Directory (DirSync) | Gestor de identidades do Forefront 2010 R2 (FIM) |Gestor de identidades do Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Repetição de escrita de dispositivos | ● |  | ● |  ||
| Atributo escrita não consolidada (para implementação híbrida do Exchange) | ● | ● | ● | ● |● |
| Repetição de escrita de utilizadores e grupos de objetos |  ●|  | |  ||
| Repetição de escrita das palavras-passe (a partir de reposição personalizada de palavra-passe (personalizada SSPR) e alterar palavra-passe) |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>Suporte de funcionalidades de autenticação

| Funcionalidade  | Ligar o Azure Active Directory | Serviços de sincronização do Azure Active Directory | Ferramenta de sincronização do Azure Active Directory (DirSync) | Gestor de identidades do Forefront 2010 R2 (FIM) |Gestor de identidades do Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| A sincronização da palavra-passe para único no local floresta AD | ● | ● | ● |  ||
| A sincronização da palavra-passe para o múltiplo no local florestas AD |  ●| ● |  |  ||
| Serviço Single Sign-on com a Federação | ● | ● | ● | ● |● |
| Repetição de escrita das palavras-passe (a partir de alteração personalizada SSPR e palavra-passe) |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>Instalação e configuração da compilação

| Funcionalidade  | Ligar o Azure Active Directory  | Serviços de sincronização do Azure Active Directory | Ferramenta de sincronização do Azure Active Directory (DirSync) | Gestor de identidades do Microsoft 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Suporta a instalação num controlador de domínio | ● | ● | ● |  |
| Suporta a instalação utilizando o SQL Express | ● | ● | ● |  |
| Fácil atualização a partir do DirSync |● |  |  |  |
| Localização da administração UX para idiomas do Windows Server | ● | ● | ● |  |
|Localização do utilizador final UX para idiomas do Windows Server| |  |  |● |
| Suporte para o Windows Server 2008 e Windows Server 2008 R2 | ● para a sincronização, não para a Federação| ● | ●  | ● |
| Suporte para o Windows Server 2012 e Windows Server 2012 R2 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>Filtragem e a configuração

Funcionalidade  | Ligar o Azure Active Directory | Serviços de sincronização do Azure Active Directory | Ferramenta de sincronização do Azure Active Directory (DirSync) | Gestor de identidades do Forefront 2010 R2 (FIM)| Gestor de identidades do Microsoft 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filtrar em domínios e unidades organizacionais | ● | ● | ● | ●  | ●
Filtrar valores de atributo dos objetos | ● | ● | ● | ●| ●
Permitir mínimo conjunto de atributos para ser sincronizado (MinSync) | ● | ● |  ||
Permitir que os modelos de serviços diferente ser aplicadas para fluxos de atributo |●  | ● |  ||
Permitir a remover atributos de fluir de AD para Azure AD | ● | ● |  |  |
Permitir personalização mais avançada para fluxos de atributo | ● | ● |  | ●  | ●

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
