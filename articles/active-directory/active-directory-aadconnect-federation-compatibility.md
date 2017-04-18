<properties
    pageTitle="Lista de compatibilidade de Federação do Azure AD"
    description="Esta página tem fornecedores de identidade não Microsoft que podem ser utilizados para implementar o início de sessão único."
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
    ms.date="09/12/2016"
    ms.author="billmath"/>

# <a name="azure-ad-federation-compatibility-list"></a>Lista de compatibilidade de Federação do Azure AD
Azure Active Directory fornece o início de sessão único no e as funcionalidades de segurança do access de aplicação para o Office 365 e outros serviços Microsoft Online para implementações e implementações apenas na nuvem sem necessidade de qualquer solução não Microsoft. Office 365, como a maioria dos Serviços Online da Microsoft, está integrado com o Azure Active Directory para serviços de directório, autenticação e autorização. Azure Active Directory também fornece serviço single sign-on para milhares de SaaS aplicações e no local aplicações web. Consulte o artigo da Galeria de aplicação do Azure Active Directory para aplicações SaaS suportadas.

Para as organizações que tenham investido soluções de Federação não Microsoft, este tópico contém orientações para configurar o serviço single sign-on para os seus utilizadores do Windows Server Active Directory com o Microsoft Online services, utilizando os fornecedores de identidades não Microsoft a partir da "Azure Active Directory Federação compatibilidade lista" abaixo. 


![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Grupo de computador Oxford](http://oxfordcomputergroup.com/), de terceiros, em nome da Microsoft, testado estes únicos início de sessão experiências utilizando os fornecedores de identidades não Microsoft contra um conjunto de casos de utilização comuns com o Azure Active Directory.

Para obter informações sobre como pode obter o seu fornecedor de identidade de terceiros listado aqui, contacte o Oxford computador Group em [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com).

>[AZURE.IMPORTANT] Grupo de computador Oxford testado apenas a funcionalidade de Federação destes cenários de início de sessão único. Oxford computador grupo não efetua qualquer testes da sincronização, autenticação de dois fatores, componentes etc destes cenários de início de sessão único.

>Utilização de início de sessão por ID alternativo para UPN também não é testada neste programa.



- [Azure Active Directory](#azure-active-directory)
- [Serviços de Federação do servidor de identidade Virtual IDM ideal](#optimal-idm-virtual-identity-server-federation-services) 
- [PingFederate 6.11](#pingfederate-611) 
- [PingFederate 7.2](#pingfederate-72) 
- [PingFederate 8](#pingfederate-8x)
- [Centrify](#centrify) 
- [Tivoli da IBM federado Gestor de identidades 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
- [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
- [AC SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
- [RadiantOne CFS 3.0](#radiantone-cfs-30) 
- [Okta](#okta) 
- [OneLogin](#onelogin) 
- [Gestor de acesso NetIQ 4.0.1](#netiq-access-manager-401) 
- [BIG-IP com o Gestor de política de acesso BIG-IP versão 11.3 x – 11.6 x](#big-ip-with-access-policy-manager-big-ip-ver-113x-116x) 
- [Portal de área de trabalho VMware versão 2.1](#vmware-workspace-portal-version-21) 
- [Iniciar sessão e aceda 5.3](#signampgo-53) 
- [IceWall Federação versão 3.0](#icewall-federation-version-30) 
- [AC segura nuvem](#ca-secure-cloud) 
- [V 7.1 do Dell um Gestor de identidade de acesso de nuvem](#dell-one-identity-cloud-access-manager-v71) 
- [Início de sessão único AuthAnvil no 4,5](#authavil-single-sign-on-45) 

>[AZURE.IMPORTANT] Uma vez que estes são os produtos de terceiros, Microsoft não fornece suporte para a implementação, configuração, resolução de problemas, melhores práticas, etc. problemas e questões relacionadas com destes fornecedores de identidade. Para obter suporte e perguntas sobre destes fornecedores de identidade, contacte diretamente a terceiros suportada.

>Destes fornecedores de identidade de terceiros foram testados para interoperabilidade com serviços em nuvem da Microsoft com WS Federação e fidedignidade WS protocolos apenas. Testes não incluiu a utilizar o protocolo SAML.

## <a name="azure-active-directory"></a>Azure Active Directory 
Azure Active Directory pode autenticar utilizadores por federar com o Active Directory no local ou sem um servidor de Federação no local através da utilização de sincronização de palavra-passe. 

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão: 


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|
|Aplicações modernas utilizando ADAL como o Office 2016| Suportada|Nenhum|

Para mais informações sobre como utilizar o Azure Active Directory com o AD FS consulte [Serviços de Federação do Active Directory (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)

Para obter mais informações sobre como utilizar o Azure Active Directory com a sincronização de palavra-passe Consulte [ligação do Azure AD](active-directory-aadconnect.md).


## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Serviços de Federação do servidor de identidade Virtual IDM ideal 
Ideal IDM Virtual identidade servidor dos serviços de Federação pode autenticar os utilizadores que residem no local Active diretórios dos clientes.

Segue-se o cenário de suporte de matriz esta experiência de início de sessão única:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Autenticação do Windows integrada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Para obter mais informações sobre o acesso de cliente diretivas consulte [limitar o acesso ao Office 365 serviços com base na localização do cliente de estação.](https://technet.microsoft.com/library/hh526961.aspx)|



## <a name="pingfederate-611"></a>PingFederate 6.11 

PingFederate 6.11 implementa o padrão de identidade WS Federation amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum (versões anteriores tem de atualizar para 6.11|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para obter as PingFederate instruções sobre como configurar esta STS para fornecer a experiência de início de sessão única aos seus utilizadores do Active Directory, transfira o pdf [aqui.](http://go.microsoft.com/fwlink/?LinkID=266321)

## <a name="pingfederate-72"></a>PingFederate 7.2 
PingFederate 7.2 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para as PingFederate obter instruções sobre como configurar esta STS fornecer a experiência de início de sessão única aos seus utilizadores do Active Directory, consulte o artigo [aqui.](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)

## <a name="pingfederate-8x"></a>PingFederate 8 
PingFederate 8 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para as PingFederate obter instruções sobre como configurar esta STS fornecer a experiência de início de sessão única aos seus utilizadores do Active Directory, consulte o artigo [aqui.](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="centrify"></a>Centrify 
Centrify ajuda a fornece uma federados única início de sessão experiência do Office 365 sem o requisito de hospedagem de um servidor de Federação no local.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Controlo de acesso de cliente não é suportado 

Para mais informações sobre Centrify, consulte o artigo [aqui.](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)|

## <a name="ibm-tivoli-federated-identity-manager-622"></a>Tivoli da IBM federado Gestor de identidades 6.2.2 
IBM Tivoli federado Gestor de identidades 6.2.2 com IBM segurança Gestor de acesso para a Microsoft aplicações 1.4 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para obter mais informações sobre IBM Tivoli federado Gestor de identidades, consulte o artigo [IBM Gestor de acesso de segurança for Applications da Microsoft.](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0 
SecureAuth IdP 7.2.0 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um quadro de intercâmbio de atributo e experiência de início de sessão único.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para mais informações sobre SecureAuth, consulte o artigo [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293).

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>AC SiteMinder 12.52 SP1 cumulativo lançamento 4
AC SiteMinder Federação 12.52 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para mais informações sobre AC SiteMinder, consulte o artigo [AC SiteMinder Federação.](http://www.ca.com/us/products/ca-single-sign-on.html) 

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0 
Serviços de Federação na nuvem (CFS) 3.0 da RadiantOne implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Autenticação do Windows integrada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para mais informações sobre RadiantOne CFS, consulte o artigo [RadiantOne CFS.](http://www.radiantlogic.com/products/radiantone-cfs/)


## <a name="okta"></a>Okta 
Okta implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única: 


| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Autenticação do Windows integrada necessita de configuração do servidor web adicionais e Okta aplicação.|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Autenticação do Windows integrada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para mais informações sobre Okta, consulte o artigo [Okta.](https://www.okta.com/)
 
## <a name="onelogin"></a>OneLogin 
OneLogin como testado no Maio de 2014 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Autenticação do Windows integrada|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Autenticação do Windows integrada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para mais informações sobre OneLogin, consulte o artigo [OneLogin.](https://www.onelogin.com/)

## <a name="netiq-access-manager-401"></a>Gestor de acesso NetIQ 4.0.1 
Gestor de acesso NetIQ 4.0.1 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |* Kerberos contratos suportados|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Autenticação do Windows integrada não é suportada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

* NetIQ suportar uma autenticação Kerberos através da configuração de um contrato Kerberos.  Para obter assistência com esta configuração, contacte o NetIQ ou visualizar o guia de configuração. Para obter mais informações sobre o Gestor de acesso NetIQ, consulte o artigo [Gestor de acesso NetIQ.](https://www.netiq.com/documentation/netiqaccessmanager4/identityserverhelp/data/b12iqp0m.html)

## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP com ver IP BIG Gestor de política de acesso. 11.3 x – 11.6 x 
IP BIG com acesso Gestor de política, (APM) ver BIG IP. 11.3 x – 11.6 x implementa o padrão de identidade do SAML amplamente utilizado para fornecer um quadro de intercâmbio de atributo e experiência de início de sessão único.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única: 

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Não é suportado |Não é suportado|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para obter mais informações sobre o Gestor de política de acesso BIG IP, consulte o artigo [Gestor de política de acesso de BIG IP.](https://f5.com/products/modules/access-policy-manager) 

Para obter instruções sobre como configurar esta o Gestor de política de acesso de BIG IP STS para fornecer a experiência de início de sessão única aos seus utilizadores do Active Directory, transfira o pdf [aqui.](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)

## <a name="vmware--workspace-portal-version-21"></a>Portal de área de trabalho VMware versão 2.1 
Portal de área de trabalho VMware versão 2.1 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Autenticação do Windows integrada não é suportada|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM | Suportada |Autenticação do Windows integrada não é suportada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para obter mais informações sobre o Portal de área de trabalho VMware versão 2.1, transfira o pdf [aqui.](http://pubs.vmware.com/workspace-portal-21/topic/com.vmware.ICbase/PDF/workspace-portal-21-resource.pdf)

## <a name="signgo-53"></a>Iniciar sessão e aceda 5.3 
Iniciar sessão e aceda 5.3 implementa a identidade WS/WS-fidedignidade de Federação amplamente utilizado padrão para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Contratos de Kerberos suportados |
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM | Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|


Início de sessão & Ir 5.3 suporta a autenticação Kerberos através da configuração de um contrato Kerberos.  Para obter assistência com esta configuração, contacte o suportehttp Ilex ou visualizar o guia de configuração [aqui.](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)


## <a name="icewall-federation-version-30"></a>IceWall Federação versão 3.0 
IceWall Federação versão 3.0 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Autenticação do Windows integrada não é suportada|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM | Suportada |Autenticação do Windows integrada não é suportada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para mais informações sobre IceWall Federação, consulte o artigo [aqui](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/) e [aqui.](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)

## <a name="ca-secure-cloud"></a>AC segura nuvem 

AC segura na nuvem implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Autenticação do Windows integrada não é suportada|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM | Suportada |Autenticação do Windows integrada não é suportada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para mais informações sobre AC segura na nuvem, consulte o artigo [AC segura na nuvem.](http://www.ca.com/us/products/security-as-a-service.aspx)

## <a name="dell-one-identity-cloud-access-manager-v71"></a>V 7.1 do Dell um Gestor de identidade de acesso de nuvem 
Gestor de acesso de nuvem Dell uma identidade implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Nenhum|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM |  Suportada |Nenhum|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|

Para obter mais informações sobre Dell uma identidade nuvem Gestor de acesso, consulte o artigo [Gestor de acesso de nuvem Dell uma identidade.](http://software.dell.com/products/cloud-access-manager)

 Para obter instruções sobre como configurar esta STS fornecer a experiência de início de sessão única aos seus utilizadores do Office 365, consulte o artigo [configurar utilizadores do Office 365.](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365) 

## <a name="authanvil-single-sign-on-45"></a>Início de sessão único AuthAnvil no 4,5 
AuthAnvil início de sessão único no 4,5 implementa o padrão de identidade WS/WS-fidedignidade de Federação amplamente utilizado para fornecer um único início de sessão no atributo exchange quadro e.

Segue-se a matriz de cenário de suporte para esta experiência de início de sessão única:

| Cliente |Suporte  |Exceções|
| --------- | --------- |--------- |
| Baseados na Web e clientes como Web Access do Exchange e SharePoint Online | Suportada |Autenticação do Windows integrada não é suportada|
| Aplicações de cliente formatado como o Lync, a subscrição do Office, CRM | Suportada |Autenticação do Windows integrada não é suportada|
| Clientes de correio eletrónico RTF como o Outlook e do ActiveSync |  Suportada |Nenhum|


Para obter mais informações, consulte o artigo [AuthAnvil Single Sign On.](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)
