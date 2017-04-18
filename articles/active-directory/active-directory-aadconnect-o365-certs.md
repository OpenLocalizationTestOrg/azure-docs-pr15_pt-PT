<properties
    pageTitle="Certificado de orientação para utilizadores do Office 365 e o Azure Active Directory renovação | Microsoft Azure"
    description="Este artigo explica como resolver problemas com a mensagens de correio eletrónico notificação-los sobre renovar um certificado para utilizadores do Office 365."
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


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar certificados de Federação para Office 365 e o Azure Active Directory

##<a name="overview"></a>Descrição geral

Para a Federação com êxito entre o Azure Active Directory (Azure AD) e os serviços de Federação do Active Directory (AD FS), os certificados utilizados pelo AD FS para assinar tokens de segurança para Azure AD devem corresponder o que é configurado no Azure AD. Qualquer erro de correspondência pode levar a fidedignidade quebrada. Azure AD assegura que estas informações são mantidas sincronizadas quando implementar o AD FS e Proxy de aplicação Web (para obter acesso extranet).

Este artigo fornece informações adicionais para gerir o seu token de certificados de assinatura e mantê-las sincronizado com o Azure AD, nos casos seguintes:

* Não está a implementar o Proxy de aplicação Web e, por isso, os metadados de Federação não está disponível na extranet.
* Não está a utilizar a configuração de predefinida do AD FS para token de certificados de assinatura.
* Está a utilizar um fornecedor de identidade de terceiros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuração predefinida do AD FS para token de certificados de assinatura

A assinatura de tokens e token desencriptar certificados são certificados normalmente autoassinados e boas de um ano. Por predefinição, o AD FS inclui um processo de renovação automática denominado **AutoCertificateRollover**. Se estiver a utilizar o AD FS 2.0 ou posterior, Office 365 e Azure AD atualizar automaticamente o seu certificado antes que expire.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notificação de renovação a partir do portal do Office 365 ou uma mensagem de e-mail

>[AZURE.NOTE] Se recebeu uma mensagem de e-mail ou uma notificação portal que lhe pede para renovar o certificado para o Office, consulte o artigo [Gerir alterações ao token de certificados de assinatura](#managecerts) para verificar se tem de efetuar qualquer ação. A Microsoft está em mente um problema possível que pode levar a notificações para renovação de certificados a ser enviada, mesmo quando não é necessária nenhuma ação.

Azure AD tentativas para monitorizar os metadados de Federação e atualizar o token de certificados de assinatura, como indicado por este metadados. 30 dias antes da expiração do token, certificados de assinatura Azure AD verifica se novos certificados estão disponíveis aos metadados de Federação de consulta.

* Se pode com êxito consultam os metadados de Federação e obter os novos certificados, sem notificação por correio eletrónico ou aviso no portal do Office 365 é emitido para o utilizador.
* Se não consegue obter o novo token de certificados de assinatura, quer porque os metadados de Federação não estão acessível ou sobreposição de certificado automática não estiver ativada, Azure AD problemas de uma notificação de e-mail e um aviso no portal do Office 365.

![Notificação de portal do Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Se estiver a utilizar o AD FS, para assegurar a continuidade do negócio, verifique se os servidores ter as seguintes atualizações, para que não ocorrem falhas de autenticação para problemas conhecidos. Isto atenua conhecidos problemas de servidor de proxy de AD FS para esta renovação e períodos de renovação futuras:
>
>Server 2012 R2 - [Windows Server rollup de Maio de 2014](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 e 2012 - [a autenticação através de proxy falha no Windows Server 2012 ou Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)

## Verificar se os certificados necessitam de ser atualizados<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>Passo 1: Verificar o estado de AutoCertificateRollover

No seu servidor do AD FS, abra o PowerShell. Verifique que o valor de AutoCertificateRollover está definido como verdadeiro.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Se estiver a utilizar o AD FS 2.0, execute primeiro adicionar Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Passo 2: Confirme que o AD FS e Azure AD estão sincronizados

No servidor ADFS, abra o pedido do Azure AD PowerShell e ligar ao Azure AD.

>[AZURE.NOTE] Pode transferir Azure AD PowerShell [aqui](https://technet.microsoft.com/library/jj151815.aspx).

    Connect-MsolService

Verifique os certificados configurados no AD FS e Azure AD propriedades para o domínio especificado de fidedignidade.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Se miniaturas em ambas as saídas de corresponderem, os seus certificados estão sincronizados com o Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Passo 3: Verificar se o seu certificado está prestes a expirar

Procurar, o resultado de obter MsolFederationProperty ou Get AdfsCertificate, a data em "Não depois." Se a data for inferior a 30 dias fora do escritório, deverá tomar ação.

| AutoCertificateRollover | Certificados sincronizados com o Azure AD | Federação metadados são publicamente acessível | Validade | Ação |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Sim | Sim | Sim | - | Não é necessária qualquer ação. Consulte o artigo [token de renovar automaticamente o certificado de assinatura](#autorenew). |
| Sim | N  | - | Menos de 15 dias | Renove imediatamente. Consulte o artigo [Renovar token certificado de assinatura manualmente](#manualrenew). |
| N | - | - | Menos de 30 dias | Renove imediatamente. Consulte o artigo [Renovar token certificado de assinatura manualmente](#manualrenew). |

\[Não importa -]

## Renovar o token de certificado de assinatura automaticamente (recomendado)<a name="autorenew"></a>

Não necessitar de efetuar quaisquer passos manuais veracidade de ambos os seguintes procedimentos:
- Ter implementado Proxy de aplicação de Web, pode ativar o acesso aos metadados Federação da extranet.
- Está a utilizar a configuração de predefinida do AD FS (AutoCertificateRollover é activado).

Verifique o seguinte para confirmar que o certificado pode ser atualizado automaticamente.

**1. a propriedade do AD FS AutoCertificateRollover tem de estar definida como verdadeiro.** Isto indica que o AD FS irá gerar automaticamente nova assinatura de tokens e certificados desencriptação token, antes da antiga uns expirarem.

**2. os metadados de Federação do AD FS estão acessível publicamente.** Certifique-se de que está acessível publicamente seus metadados de Federação ao navegar para o seguinte URL de um computador na internet pública (fora de rede da empresa):


/federationmetadata/2007-06/federationmetadata.xml https:// (your_FS_name)

onde `(your_FS_name) `é substituído com o nome de anfitrião de serviços de Federação sua organização utiliza, tal como fs.contoso.com.  Se for possível verificar ambos destas definições com êxito, não tem de fazer mais nada.  

Exemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renovar o token de certificado de assinatura manualmente<a name="manualrenew"></a>

Pode escolher renovar o token de certificados de assinatura manualmente. Por exemplo, os seguinte cenários funcionará melhor para renovação manual:
* Tokens de certificados de assinatura são certificados não autoassinados. O mais comuns motivo para isto é que a sua organização gere certificados de AD FS inscritos a partir de uma autoridade de certificação organizacional.
* Segurança da rede não permite os metadados de Federação esteja disponível publicamente.

Nestes cenários, sempre que atualizar o token de assinatura certificados, também terá de actualizar o domínio do Office 365 utilizando o comando PowerShell, MsolFederatedDomain de atualização.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Passo 1: Certifique-se de que o AD FS tem novo token de certificados de assinatura

**Configuração não predefinida**

Se estiver a utilizar uma configuração não predefinida do AD FS (onde **AutoCertificateRollover** está definido como **Falso**), provavelmente está a utilizar certificados personalizados (não personalizada tiver sessão iniciados). Para mais informações sobre como renovar o token de AD FS certificados de assinatura, consulte [orientações para os clientes que não utilizam o AD FS personalizada assinado certificados](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Federação metadados não está disponível ao público**

Por outro lado, se **AutoCertificateRollover** está definida como **Verdadeiro**, mas os seus metadados de Federação não está acessível publicamente, primeiro certifique-se de que foram gerados certificados de assinatura de tokens novos pelo AD FS. Confirme que tem o novo token de certificados de assinatura ao efetuar os seguintes passos:

1. Certifique-se de que iniciou sessão no servidor ADFS principal.
2. Verifique os certificados de assinatura atuais no AD FS ao abrir uma janela de comando do PowerShell e executando o seguinte comando:

    PS c:\>assinatura de tokens Get-ADFSCertificate – CertificateType

    >[AZURE.NOTE] Se estiver a utilizar o AD FS 2.0, deve executar adicionar Pssnapin Microsoft.Adfs.Powershell pela primeira vez.

3. Observe a saída do comando em qualquer certificados listados. Se o AD FS tiver gerado um novo certificado, deverá ver dois certificados no resultado: uma para o qual o valor de **IsPrimary** é **Verdadeiro** e a data de **não depois** é dentro de 5 dias e uma para o qual **IsPrimary** é **Falso** e **não depois** é de cerca de um ano no futuro.

4. Se vir apenas um certificado e a data de **não depois** é dentro de 5 dias, tem de gerar um certificado novo.

5. Para gerar um novo certificado, execute o seguinte comando na linha de comandos do PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Verifique se a atualização, executando o seguinte comando novamente: c PS:\>assinatura de tokens Get-ADFSCertificate – CertificateType

Dois certificados devem ser indicados agora, um dos quais tem uma data de **não depois** de cerca de um ano no futuro e para o qual o valor de **IsPrimary** é **Falso**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Passo 2: Atualizar o novo token de certificados para a fidedignidade do Office 365 de assinatura

Atualize o Office 365 com o novo token de certificados para ser utilizado para a fidedignidade, da seguinte forma de assinatura.

1.  Abra o módulo do Microsoft Azure Active Directory para Windows PowerShell.
2.  Executar $cred = Get-credenciais. Quando este cmdlet pede-lhe para introduzir as credenciais, escreva as credenciais de conta de administrador de serviço de nuvem.
3.  Executar ligar MsolService – $cred de credenciais. Este cmdlet irão ligá-lo para o serviço de nuvem. Criação de um contexto que irão ligá-lo para o serviço de nuvem é necessária antes de executar qualquer uma dos cmdlets adicionais instalados pela ferramenta.
4.  Se estiver a executar estes comandos num computador que não seja o servidor de Federação principal do AD FS, executar conjunto MSOLAdfscontext-computador <AD FS primary server>, onde <AD FS primary server> é o nome FQDN interno do servidor do AD FS principal. Este cmdlet cria um contexto que irão ligá-lo para o AD FS.
5.  Executar a atualização MSOLFederatedDomain – NomeDomínio <domain>. Este cmdlet atualiza as definições a partir do AD FS no serviço de nuvem e configura a relação de confiança entre as duas.


>[AZURE.NOTE] Se precisar de suporte de múltiplos domínios de nível superiores, como contoso.com e fabrikam.com, tem de utilizar o parâmetro **SupportMultipleDomain** com qualquer cmdlets. Para mais informações, consulte o artigo [suporte para múltiplos domínios de nível superior](active-directory-aadconnect-multiple-domains.md).

## Reparar fidedignidade Azure AD utilizando a ligação do Azure AD<a name="connectrenew"></a>

Se tiver configurado o seu farm do AD FS e fidedignidade Azure AD utilizando a ligação do Azure AD, pode utilizar a ligação do Azure AD para detetar se precisar de levar qualquer ação para o token de certificados de assinatura. Se precisar de renovar os certificados, pode utilizar a ligação do Azure AD para fazê-lo.

Para mais informações, consulte [reparar a fidedignidade](./active-directory-aadconnect-federation-management.md#repairing-the-trust).
