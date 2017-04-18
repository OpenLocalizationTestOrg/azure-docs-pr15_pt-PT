<properties
    pageTitle="Ligação do Azure AD vários domínios"
    description="Este documento descreve como instalar e configurar vários domínios de nível superior com o Office 365 e o Azure AD."
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

# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Suporte de múltiplos de domínio para federar com Azure AD
A seguinte documentação fornece orientações sobre como utilizar vários domínios de nível superior e subdomínios quando federar com o Office 365 ou domínios do Azure AD.

## <a name="multiple-top-level-domain-support"></a>Suporte de múltiplos domínio de topo
Federar várias, domínios de nível superior com Azure AD requer alguns configuração adicionais que não é necessária quando federar com um domínio de nível superior.

Quando um domínio for Federado com Azure AD, várias propriedades são definidas no domínio no Azure.  Um importantes é a IssuerUri.  Este é um URI que é utilizado pelo Azure AD para identificar o domínio que está associado ao token de.  O URI não necessite de ser resolvido para qualquer, mas tem de ser um URI válido.  Por predefinição, Azure AD define o isto para o valor do identificador de serviços de Federação na sua no local AD FS configuração.

>[AZURE.NOTE]O identificador de serviços de Federação é um URI que identifica um serviço de Federação.  O serviço de Federação é uma instância do AD FS que funciona como o serviço de tokens de segurança. 

Pode ver IssuerUri utilizando o comando PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

Um problema surge quando queremos adicionar mais do que um domínio de nível superior.  Por exemplo, vamos supor tiver Federação de configuração entre Azure AD e o seu ambiente no local.  Para este documento estiver a utilizar o bmcontoso.com.  Agora posso ter adicionado um domínio de segundo de nível superior, bmfabrikam.com.

![Domínios](./media/active-directory-multiple-domains/domains.png)

Quando vamos tentar converter o nosso domínio bmfabrikam.com ser federada, podemos receber um erro.  O motivo para isto é, Azure AD tem um constrangimento que não permite a propriedade IssuerUri de ter o mesmo valor para o mais do que um domínio.  
  

![Erro de Federação](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>Parâmetro de SupportMultipleDomain

Para contornar esta situação, precisamos de adicionar um IssuerUri diferentes que pode ser efetuada ao utilizar o `-SupportMultipleDomain` parâmetro.  Este parâmetro é utilizado com os seguintes cmdlets:
    
- `New-MsolFederatedDomain`
- `Convert-MsolDomaintoFederated`
- `Update-MsolFederatedDomain`

Este parâmetro torna Azure AD configurar a IssuerUri para que baseia-se o nome do domínio.  Este será exclusivo ao longo de diretórios no Azure AD.  Utilizar o parâmetro permite que o comando do PowerShell para concluída com êxito.

![Erro de Federação](./media/active-directory-multiple-domains/convert.png)

Consultar as definições do nosso novo domínio bmfabrikam.com pode ver o seguinte:

![Erro de Federação](./media/active-directory-multiple-domains/settings.png)

Tenha em atenção que `-SupportMultipleDomain` não altera as outros pontos finais de que ainda estão configurados para apontarem para o nosso serviço de Federação em adfs.bmcontoso.com.

Outra coisa que `-SupportMultipleDomain` é que é que assegura que o sistema de AD FS inclui o valor inicial do emissor em tokens emitidos para Azure AD. Faz isto obtendo na parte dos utilizadores UPN do domínio e esta definição, como o domínio no IssuerUri, ou seja, https://{upn sufixo} / serviços/adfs/fidedignidade. 

Assim durante a autenticação do Azure AD ou Office 365, o elemento IssuerUri no token do utilizador é utilizada para localizar o domínio no Azure AD.  Se uma correspondência não é possível localizar que a autenticação irá falhar. 

Por exemplo, se um utilizador estiverem UPN é bsimon@bmcontoso.com, o elemento IssuerUri os problemas do token AD FS será definido como http://bmcontoso.com/adfs/services/trust. Isto irá a corresponder à configuração do Azure AD e autenticação será bem sucedida.

Segue-se a regra de afirmação personalizada que implementa esta lógica:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type =   "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


>[AZURE.IMPORTANT]Para poder utilizar o parâmetro - SupportMultipleDomain quando tentar adicionar novo ou já a converter domínios adicionados, tem de ter o programa de configuração do seu fidedignidade federada para suportá-los originalmente.  


## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Como atualizar a fidedignidade entre o AD FS e Azure AD
Se não de configuração a fidedignidade federada entre o AD FS e a sua instância do Azure AD, poderá ter de voltar a criar esta fidedignidade.  Esta é uma vez que, quando for originalmente configuração sem o `-SupportMultipleDomain` parâmetro, o IssuerUri está definido com o valor predefinido.  Captura de ecrã abaixo pode ver que o IssuerUri está definido para https://adfs.bmcontoso.com/adfs/services/trust.

Por isso, agora, se podemos com êxito ter adicionado um novo domínio no portal do Azure AD e, em seguida, tentar convertê-lo utilizando `Convert-MsolDomaintoFederated -DomainName <your domain>`, podemos obtém o seguinte erro.

![Erro de Federação](./media/active-directory-multiple-domains/trust1.png)

Se tentar adicionar a `-SupportMultipleDomain` mudar podemos vai receber a seguinte mensagem de erro:

![Erro de Federação](./media/active-directory-multiple-domains/trust2.png)

Basta tentar executar `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` no domínio original também irá resultar num erro.

![Erro de Federação](./media/active-directory-multiple-domains/trust3.png)

Utilize os passos abaixo para adicionar um domínio adicional de nível superior.  Se já tiver adicionado um domínio e não utilizou o `-SupportMultipleDomain` começam de parâmetro com os passos para remover e atualizar o seu domínio original.  Se não tiver adicionado um domínio de nível superior ainda pode começar com os passos para adicionar um domínio utilizando o PowerShell do Azure AD Connect.

Utilize os passos seguintes para remover a fidedignidade do Microsoft Online e atualizar o seu domínio original.

2.  No seu servidor de Federação do AD FS abrir **Gestão do AD FS.** 
2.  À esquerda, expandir **Relações de fidedignidade** e **Colocar festa fidedignidades**
3.  À direita, elimine a entrada de **Plataforma de identidade do Microsoft Office 365** .
![Remover o Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
1.  Num computador que tenha instalado o [Módulo Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , execute o seguinte: `$cred=Get-Credential`.  
2.  Introduza o nome de utilizador e palavra-passe de administrador global para o domínio do Azure AD que são federar com
2.  No PowerShell introduza`Connect-MsolService -Credential $cred`
4.  No PowerShell introduza `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`.  Destina-se para o domínio original.  Por isso, utilizar os domínios acima seria:`Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`


Utilize os seguintes passos para adicionar o novo domínio de nível superior através do PowerShell

1.  Num computador que tenha instalado o [Módulo Azure Active Directory para Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) , execute o seguinte: `$cred=Get-Credential`.  
2.  Introduza o nome de utilizador e palavra-passe de administrador global para o domínio do Azure AD que são federar com
2.  No PowerShell introduza`Connect-MsolService -Credential $cred`
3.  No PowerShell introduza`New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

Utilize os seguintes passos para adicionar o domínio de nível superior novo utilizando a ligação do Azure AD.

1.  Ligação do Azure AD do ambiente de trabalho de iniciação ou o menu Iniciar
2.  Selecione "Adicionar um domínio adicional de AD Azure" ![adicionar uma adicional domínio do Azure AD](./media/active-directory-multiple-domains/add1.png)
3.  Introduza o seu Azure AD e credenciais do Active Directory
4.  Selecione o domínio de segundo que pretende configurar para a Federação.
![Adicionar uma adicional domínio do Azure AD](./media/active-directory-multiple-domains/add2.png)
5.  Clique em instalar


### <a name="verify-the-new-top-level-domain"></a>Verifique se o novo domínio de nível superior
Utilizando o comando PowerShell `Get-MsolDomainFederationSettings - DomainName <your domain>`pode ver o IssuerUri atualizado.  A captura de ecrã abaixo mostra a Federação definições foram atualizadas no nosso http://bmcontoso.com/adfs/services/trust domínio original

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

E IssuerUri no nosso novo domínio tiver sido definida para https://bmfabrikam.com/adfs/services/trust

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)


##<a name="support-for-sub-domains"></a>Suporte para subdomínios
Quando adiciona um subdomínio, devido aos domínios de forma Azure AD resolvido, irá herdar as definições de principal.  Isto significa que o IssuerUri tem de corresponder os elementos principais.

Por isso, vamos apresentamos por exemplo, posso ter bmcontoso.com e, em seguida, adicionar corp.bmcontoso.com.  Isto significa que IssuerUri para um utilizador a partir do corp.bmcontoso.com terão de ser **http://bmcontoso.com/adfs/services/trust.**  No entanto, a regra padrão implementada acima para Azure AD, irá gerar um token com um emissor como **http://corp.bmcontoso.com/adfs/services/trust.** que não irá corresponder o domínio necessário valor e a autenticação irá falhar.

### <a name="how-to-enable-support-for-sub-domains"></a>Como ativar o suporte para subdomínios
Para contornar este o AD FS dependente fidedignidade de terceiros para Microsoft Online tem de ser atualizados.  Para fazer isto, tem de configurar uma regra de afirmações personalizadas para que revelar desativar qualquer subdomínios a partir do sufixo UPN do utilizador quando construir o valor de emissor personalizado. 

A seguinte afirmação irá faça o seguinte:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));

Utilize os seguintes passos para adicionar uma afirmação personalizada para suportar subdomínios.

1.  Abrir AD FS gestão
2.  Clique com o botão direito do rato em fidedignidade RP Online da Microsoft e selecione editar afirmação regras
3.  Selecione a regra afirmação terceira e substituir ![afirmação editar](./media/active-directory-multiple-domains/sub1.png)
4.  Substitua a afirmação atual:
    
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
        
    com
    
        `c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^((.*)([.|@]))?(?<domain>[^.]*[.].*)$", "http://${domain}/adfs/services/trust/"));`
    
![Substituir afirmação](./media/active-directory-multiple-domains/sub2.png)
5.  Clique em Ok.  Clique em aplicar.  Clique em Ok.  Feche a gestão do AD FS.

