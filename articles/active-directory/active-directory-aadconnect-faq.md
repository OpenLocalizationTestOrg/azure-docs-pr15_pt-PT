<properties
    pageTitle="Ligação do Azure AD: Perguntas mais frequentes sobre | Microsoft Azure"
    description="Esta página tem perguntas mais frequentes sobre a ligação do Azure AD."
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
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-faq"></a>FAQ de ligação do Azure AD

## <a name="general-installation"></a>Gerais de instalação
**P: instalação funcionam se o Administrador Global do AD Azure tem 2FA ativado?**  
Com compilações a partir de Fevereiro de 2016, isto é suportado.

**P: existe uma forma para instalar a ligação do Azure AD-prima automático?**  
Só é suportada para instalar a ligação do Azure AD utilizando o Assistente de instalação. Uma instalação automática e silenciosa não é suportada.

**P: Posso ter uma floresta onde não é possível contactar um domínio. Como posso instalar a ligação do Azure AD?**  
Com compilações a partir de Fevereiro de 2016, isto é suportado.

**P: o agente de estado de funcionamento do AD DS funciona na core servidor?**  
Sim. Depois de instalar o agente, pode concluir o processo de registo utilizando o PowerShell commandlet seguinte: 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

## <a name="network"></a>Rede
**P: Posso ter uma firewall, o dispositivo de rede ou outra coisa que limita as ligações de tempo máximo pode manter-se abrir na minha rede. Quanto tempo meu limiar de tempo limite de lado do cliente deverá ao utilizar a ligação do Azure AD?**  
Todo o software de rede, dispositivos físicos ou mais nada que limita o tempo máximo de ligações podem permanecer abertas deve utilizar um limite de pelo menos 5 minutos (300 segundos) para a conectividade entre o servidor onde está instalado o cliente de ligação do Azure AD e o Azure Active Directory. Também se aplica a todas as ferramentas de sincronização do Microsoft Identity disponibilizadas anteriormente.

**P: são suportadas SLDs (única etiqueta de domínios)?**  
Não, Azure AD Connect não suporta a no local florestas/domínios utilizando SLDs.

**P: são "ponteada que indica" NetBios denominada suportadas?**  
Não, Azure AD Connect não suporta florestas/domínios de no local onde o nome NetBios contém um período "." no nome.

## <a name="federation"></a>Federação
**P: o que posso fazer se recebo uma mensagem de e-mail que pedir-me para renovar a minha certificado do Office 365**  
Utilize as informações que está destacada ao tópico sobre como renovar o certificado de [renovar certificados](active-directory-aadconnect-o365-certs.md) .

**P: Posso ter "Atualizar automaticamente parte dependente" Configurar para a parte dependente do Office 365. Tenho de ter para efetuar qualquer ação quando meu token de certificado de assinatura automaticamente descer?**  
Utilize as informações que está destacada ao artigo [renovar certificados](active-directory-aadconnect-o365-certs.md).

## <a name="environment"></a>Ambiente
**P: é suportado para mudar o nome do servidor após ter sido instalado a ligação do Azure AD?**  
Não. Mudar o nome do servidor provocará motor de sincronização para não conseguir ligar à base de dados SQL e o serviço não conseguir iniciar.

## <a name="identity-data"></a>Dados de identidade
**P: o atributo UPN (userPrincipalName) no Azure AD não corresponder o UPN no local - por que motivo?**  
Consulte estes artigos:

- [Nomes de utilizador no Office 365, Azure ou Intune não corresponderem o UPN no local ou o ID de início de sessão alternativo](https://support.microsoft.com/en-us/kb/2523192)
- [Alterações não são sincronizadas pela ferramenta de sincronização do Azure Active Directory depois de alterar o UPN de uma conta de utilizador para utilizar um domínio diferente federado](https://support.microsoft.com/en-us/kb/2669550)

Também pode configurar Azure AD para permitir que o motor de sincronização atualizar o userPrincipalName conforme descrito na [ligação do Azure AD funcionalidades do serviço de sincronização](active-directory-aadconnectsyncservice-features.md).

## <a name="custom-configuration"></a>Configuração personalizada
**P: onde estão os cmdlets do PowerShell para a ligação do Azure AD-se documentados?**  
Com a exceção dos cmdlets documentados neste site, outros os cmdlets do PowerShell encontrados na ligação do Azure AD não são suportadas para utilização do cliente.

* *P: posso utilizar "exportação/de servidor de importar" que se encontram no *Gestor de serviço de sincronização* para mover configuração entre servers? * *  
Não. Esta opção não irá obter todas as definições de configuração e não deve ser utilizada. Em vez disso, deve utilizar o Assistente para criar a configuração base no segundo servidor e utilizar o editor de regras de sincronização para gerar scripts de PowerShell para mover qualquer regra personalizada entre servidores. Consulte o artigo [Mover configuração personalizada a partir do active para servidor de teste](active-directory-aadconnect-upgrade-previous-version.md#move-custom-configuration-from-active-to-staging-server).

## <a name="troubleshooting"></a>Resolução de problemas
**P: como posso obter ajuda com ligação do Azure AD?**

[Procurar na Base de dados de conhecimento da Microsoft (KB)](https://www.microsoft.com/en-us/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

- Procure a Base de dados de conhecimento de Microsoft (KB) técnico soluções para problemas comuns de quebra fix acerca do suporte para a ligação do Azure AD.

[Fóruns do Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

- Pode procurar e navegue para técnicos as perguntas e respostas da Comunidade ou faça a sua própria pergunta ao clicar em [aqui](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Suporte ao cliente Azure AD Connect](https://manage.windowsazure.com/?getsupport=true)

- Utilize esta ligação para obter suporte através do portal do Azure.
