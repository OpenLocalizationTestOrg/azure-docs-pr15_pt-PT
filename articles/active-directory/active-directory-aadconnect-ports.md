<properties
    pageTitle="Ligação do Azure AD: Portas | Microsoft Azure"
    description="Esta página é uma página de referência técnica para portas que são necessários para ser aberto para ligação do Azure AD"
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
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>Híbrido identidade obrigatório portas e protocolos
O documento seguinte é uma referência técnica para fornecer informações sobre as portas e protocolos necessários que são necessários para implementar uma solução de identidade híbrido. Utilize a seguinte ilustração e consulte a tabela correspondente.

![O que é a ligação do Azure AD](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabela 1 - Azure AD ligar e no local AD
A tabela seguinte descreve as portas e protocolos que são necessários para a comunicação entre o servidor de ligação do Azure AD e no local AD.

Protocolo | Portas | Descrição
--------- | --------- |---------
DNS|53 (TCP/UDP RECOMENDADAS)| Pesquisas DNS na floresta de destino.
Kerberos|88 (TCP/UDP RECOMENDADAS)| Autenticação Kerberos à floresta AD.
MS RPC |135 (TCP/UDP RECOMENDADAS)| Utilizado durante a configuração inicial do Assistente de ligação do Azure AD quando liga-se para a floresta AD.
LDAP|389 (TCP/UDP RECOMENDADAS)| Utilizado para importar dados a partir do AD. Os dados são encriptados com o sinal de Kerberos & junta.
LDAP/SSL|636 (TCP/UDP RECOMENDADAS)| Utilizado para importar dados a partir do AD. A transferência de dados está assinada e encriptada. Utilizado apenas se estiver a utilizar SSL.
RPC |49152 a 65535 (aleatório alta RPC Port)(TCP/UDP)| Utilizado durante a configuração inicial da ligação do Azure AD quando liga-se para as florestas AD. Consulte o artigo [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)e [KB224196](https://support.microsoft.com/kb/224196) para obter mais informações.

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabela 2 - Azure AD ligar e Azure AD
A tabela seguinte descreve as portas e protocolos que são necessários para a comunicação entre o servidor de ligação do Azure AD e Azure AD.

Protocolo |Portas |Descrição
--------- | --------- |---------
HTTP|80 (TCP/UDP RECOMENDADAS)| Utilizados para transferir CRLs (listas revogação de certificado) para confirmar os certificados SSL.
HTTPS|443(TCP/UDP)| Utilizada para sincronizar com o Azure AD.

Para obter uma lista de URLs e IP endereços tem de o abrir no seu firewall, consulte [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>Tabela 3 - Azure AD ligar e os servidores de Federação/WAP
A tabela seguinte descreve as portas e protocolos que são necessários para a comunicação entre o servidor de ligação do Azure AD e os servidores de Federação/WAP.  

Protocolo |Portas |Descrição
--------- | --------- |---------
HTTP|80 (TCP/UDP RECOMENDADAS)| Utilizados para transferir CRLs (listas revogação de certificado) para confirmar os certificados SSL.
HTTPS|443(TCP/UDP)| Utilizada para sincronizar com o Azure AD.
WinRM|5985| Escuta do WinRM

## <a name="table-4---wap-and-federation-servers"></a>Tabela 4 - WAP e os servidores de Federação
A tabela seguinte descreve as portas e protocolos que são necessários para comunicações entre servidores de Federação e os servidores de WAP.

Protocolo |Portas |Descrição
--------- | --------- |---------
HTTPS|443(TCP/UDP)| Utilizado para autenticação.

## <a name="table-5---wap-and-users"></a>Tabela 5 - WAP e utilizadores
A tabela seguinte descreve as portas e protocolos que são necessários para a comunicação entre os utilizadores e os servidores WAP.

Protocolo |Portas |Descrição
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Utilizado para autenticação de dispositivo.
TCP|49443 (TCP)| Utilizado para autenticação de certificados.

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 6a & 6º b - agente do Azure AD ligar-se do Estado de funcionamento para (AD FS/Sync) e Azure AD
As tabelas seguintes descrevem os pontos finais, portas e protocolos que são necessários para a comunicação entre agentes do Azure AD ligar-se do Estado de funcionamento e Azure AD

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabela 6a - portas e protocolos para agente do Azure AD ligar-se do Estado de funcionamento para (AD FS/Sync) e Azure AD
A tabela seguinte descreve as seguintes saídas portas e protocolos que são necessários para comunicação entre o agentes do Azure AD ligar-se do Estado de funcionamento e Azure AD.  

Protocolo |Portas  |Descrição
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Saída
Serviço Azure Bus|5671 (TCP/UDP RECOMENDADAS)| Saída

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6º b - os pontos finais para agente do Azure AD ligar-se do Estado de funcionamento para (AD FS/Sync) e Azure AD
Para obter uma lista dos pontos finais, consulte [a secção requisitos para o agente do Azure AD ligar-se do Estado de funcionamento](active-directory-aadconnect-health-agent-install.md#requirements).
