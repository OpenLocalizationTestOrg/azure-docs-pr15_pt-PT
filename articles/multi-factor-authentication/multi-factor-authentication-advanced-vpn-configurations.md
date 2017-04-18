<properties
    pageTitle="Cenários avançados com autenticação Multifator Azure e 3º VPNs festas"
    description="Esta página fornece informações sobre passo a passo da configuração para Azure MFA com 3º produtos de terceiros."
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
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-3rd-party-vpn"></a>Cenários avançados com autenticação Multifator Azure e 3º VPN festa
Autenticação Multifator Azure pode ser utilizada de forma totalmente integrada estabelecer ligação com uma variedade de 3º festa VPN soluções.  Isto inclui aparelho Cisco® ASA VPN, aparelho Citrix NetScaler SSL VPN e aparelho zimbro redes seguro Pulse/acesso seguro ligar seguro SSL VPN.

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Aparelho de ASA VPN Cisco e Azure a autenticação Multifator
Autenticação Multifator Azure integra-se totalmente com o seu aparelho Cisco® ASA VPN para fornecer segurança adicional para os inícios de sessão Cisco AnyConnect® VPN e acesso ao portal.  Isto pode ser feito utilizando o LDAP ou RADIUS protocolo.  Selecione um dos seguintes procedimentos para transferir as guias de instruções passo a passo detalhado da configuração.

Guia de configuração  | Descrição
------------- | ------------- |
[Cisco ASA com a configuração de MFA AnyConnect da VPN e Azure para LDAP](http://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrar totalmente o seu aparelho Cisco ASA VPN com MFA Azure utilizando LDAP|
[Cisco ASA com a configuração de MFA AnyConnect da VPN e Azure para RADIUS](http://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrar totalmente o seu aparelho Cisco ASA VPN com MFA Azure utilizar RADIUS

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>Citrix NetScaler SSL VPN e Azure autenticação Multifator
Autenticação Multifator Azure integra-se totalmente com o seu aparelho Citrix NetScaler SSL VPN para fornecer segurança adicional para os inícios de sessão Citrix NetScaler SSL VPN e acesso ao portal.  Isto pode ser feito utilizando o LDAP ou RADIUS protocolo.  Selecione um dos seguintes procedimentos para transferir as guias de instruções passo a passo detalhado da configuração.

Guia de configuração  | Descrição
------------- | ------------- |
[Configuração de MFA Citrix NetScaler SSL VPN e Azure para LDAP](http://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrar totalmente o VPN de SSL NetScaler Citrix com aparelho Azure MFA utilizando LDAP|
[Configuração do MFA Citrix NetScaler SSL VPN e Azure para RADIUS](http://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrar totalmente o seu aparelho Citrix NetScaler SSL VPN com MFA Azure utilizar RADIUS

##<a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Aparelho zimbro/Pulse seguro SSL VPN e Azure a autenticação Multifator
Autenticação Multifator Azure integra-se totalmente com o seu aparelho zimbro/Pulse seguro SSL VPN para fornecer segurança adicional para os inícios de sessão zimbro/Pulse seguro SSL VPN e acesso ao portal.  Isto pode ser feito utilizando o LDAP ou RADIUS protocolo.  Selecione um dos seguintes procedimentos para transferir as guias de instruções passo a passo detalhado da configuração.

Guia de configuração  | Descrição
------------- | ------------- |
[Configuração de MFA zimbro/Pulse seguro SSL VPN e Azure para LDAP](http://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx)| Integrar totalmente o VPN do zimbro/Pulse seguro SSL com aparelho Azure MFA utilizando LDAP|
[Configuração do MFA zimbro/Pulse seguro SSL VPN e Azure para RADIUS](http://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrar totalmente o seu aparelho zimbro/Pulse seguro SSL VPN com MFA Azure utilizar RADIUS
