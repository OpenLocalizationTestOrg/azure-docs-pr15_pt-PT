<properties
    pageTitle="Azure Active Directory Domain Services: Funcionalidades | Microsoft Azure"
    description="Funcionalidades do Azure Active Directory Domain Services"
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
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Serviços de domínio do Azure AD

## <a name="features"></a>Funcionalidades
As seguintes funcionalidades estão disponíveis nos serviços de domínio do Azure AD geridos domínios.

- **Experiência de implementação simples:** Pode ativar serviços de domínio do Azure AD do seu inquilino do Azure AD com apenas alguns cliques. Independentemente de se o seu inquilino do Azure AD for um inquilino na nuvem ou sincronizado com o seu diretório no local, o seu domínio gerido pode aprovisionado rapidamente.

- **Suporte para o domínio associação:** Pode facilmente computadores de associação de domínio na rede virtual Azure que está disponível no seu domínio gerido. A experiência de associação de domínio no cliente do Windows e funciona de sistemas operativos de servidor totalmente contra domínios servidos pelos serviços de domínio do Azure AD. Também pode utilizar a associação de domínio automatizado ferramenta contra desses domínios.

- **Instância de um domínio por diretório do Azure AD:** Pode criar um domínio do Active Directory único para cada directório Azure AD.

- **Criar domínios com nomes personalizados:** Pode criar domínios com nomes personalizados (por exemplo, ' contoso100.com') utilizando os serviços de domínio do Azure AD. Pode utilizar qualquer um dos nomes de domínio verificado ou não verificado. Opcionalmente, também pode criar um domínio com o sufixo de domínio incorporados (ou seja, ' *. onmicrosoft.com') oferecidos pelo seu diretório do Azure AD.

- **Integrado com o Azure AD:** Não tem de configurar ou gerir a replicação para serviços de domínio do Azure AD. Contas de utilizador, os membros do grupo e as credenciais de utilizador (palavras-passe) a partir do diretório da sua Azure AD são automaticamente disponíveis nos serviços de domínio do Azure AD. Novos utilizadores, grupos ou alterações a atributos a partir do seu inquilino do Azure AD ou o seu diretório no local são sincronizadas automaticamente para serviços de domínio do Azure AD.

- **Autenticação NTLM e Kerberos:** Com o suporte para autenticação NTLM e Kerberos, pode implementar aplicações que dependem de autenticação integrada do Windows.

- **Utilizar empresarial credenciais/palavras-passe:** Palavras-passe para os utilizadores no seu inquilino do Azure AD funcionam com serviços de domínio do Azure AD. Os utilizadores podem utilizar as respetivas credenciais para máquinas de associação de domínio empresariais, inicie sessão na forma interativa ou ao longo do ambiente de trabalho remoto e autenticar o domínio gerido.

- **Enlace LDAP & LDAP ler suporte:** Pode utilizar as aplicações que dependem de enlaces LDAP para autenticar os utilizadores na domains servidos pelos serviços de domínio do Azure AD. Para além disso, as aplicações que utilizam LDAP operações de leitura para atributos de computador do utilizador/da consulta do diretório também podem trabalhar contra dos serviços de domínio do Azure AD.

- **Seguro LDAP (LDAPS):** Pode ativar o acesso ao diretório sobre seguro LDAP (LDAPS). Um acesso seguro LDAP está disponível no interior da rede virtual por predefinição. No entanto, opcionalmente, também pode ativar o acesso LDAP seguro através da internet.

- **Política de grupo:** Pode utilizar um único incorporado GPO cada para os utilizadores e computadores contentores para impor a conformidade com necessário políticas de segurança para contas de utilizador e computadores associados ao domínio.

- **Gerir DNS:** Os membros do grupo 'AAD CC administradores' podem gerir DNS do seu domínio gerido utilizando ferramentas de administração do DNS familiares como o snap-in MMC de administração de DNS.

- **Criar personalizado unidades organizacionais (ou):** Os membros do grupo 'AAD CC administradores' podem criar personalizado ou no domínio gerido. Estes utilizadores são concedidas privilégios administrativos totais sobre personalizado ou, para que estes podem adicionar/remover contas de serviço, computadores, grupos, etc. dentro estes ou personalizado.

- **Disponível em regiões Azure múltiplos:** Ver a página de [Serviços Azure por região](https://azure.microsoft.com/regions/#services/) saber as regiões Azure na qual dos serviços de domínio do Azure AD está disponível.

- **Elevada disponibilidade:** Serviços de domínio do Azure AD oferece elevada disponibilidade para o seu domínio. Esta funcionalidade oferece garantia do tempo de funcionamento de serviço mais elevado e resistência a falhas. Estado de funcionamento incorporado monitorizar ofertas automatizado remediação de falhas por girar por novas instâncias para substituir instâncias falhadas e para fornecer o serviço continuação do seu domínio.

- **Utilizar ferramentas de gestão familiares:** Pode utilizar ferramentas de gestão do Active Directory do Windows Server familiares como o Centro de administração do Active Directory ou o Active Directory PowerShell para administrar domínios geridos.
