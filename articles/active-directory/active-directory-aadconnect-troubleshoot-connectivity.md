<properties
    pageTitle="Ligação do Azure AD: Resolver problemas de conectividade | Microsoft Azure"
    description="Explica como resolver problemas de conectividade com a ligação do Azure AD."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/27/2016"
    ms.author="billmath"/>

# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Resolver problemas de conectividade com a ligação do Azure AD
Este artigo explica como funciona a conectividade entre ligação do Azure AD e Azure AD e como resolver problemas de conectividade. Estes problemas são mais provável ser visto num ambiente com um servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Resolução de problemas de conectividade no Assistente de instalação
Ligação do Azure AD está a utilizar para a autenticação de autenticação moderna (utilizando a biblioteca de ADAL). O Assistente de instalação e o motor de sincronização inicial requerem Machine. config para estar corretamente configurados uma vez que estes são aplicações .NET.

Neste artigo iremos mostrar como Fabrikam liga-se ao Azure AD através do servidor de proxy. O servidor proxy com o nome fabrikamproxy e estiver a utilizar porta 8080.

Em primeiro lugar precisamos de Certifique-se de [**Machine. config**](active-directory-aadconnect-prerequisites.md#connectivity) está configurado corretamente.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

>[AZURE.NOTE]
Em algumas blogues não Microsoft é documentado que devem ser efetuadas alterações para miiserver.exe.config em vez disso. No entanto, este ficheiro é substituído no cada atualização por isso, mesmo se funciona durante a instalação inicial, o sistema para de funcionar no primeira atualização. Por que motivo a recomendação é atualizar Machine. config em vez disso.

O servidor proxy também tem de ter os URLs necessários abertos. A lista oficial é documentada no [Office 365 URLs e intervalos de endereços IP ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Destas opções, a tabela seguinte é as condições absoluta mínimas para conseguir ligar ao Azure AD de todo. Esta lista não inclui qualquer funcionalidades opcionais, como a palavra-passe de escrita não consolidada ou Azure AD ligar-se do Estado de funcionamento. É documentado aqui para ajudar na resolução de problemas para a configuração inicial.

URL | Porta | Descrição
---- | ---- | ----
mscrl.microsoft.com | HTTP/80 | Utilizados para Transferir CRL listas.
\*. verisign.com | HTTP/80 | Utilizados para Transferir CRL listas.
\*. entrust.com | HTTP/80 | Utilizados para Transferir CRL listas para MFA.
\*. windows.net | HTTPS/443 | Utilizado para iniciar sessão no Azure AD.
Secure.aadcdn.microsoftonline p.com | HTTPS/443 | Utilizado para MFA.
\*. microsoftonline.com | HTTPS/443 | Utilizado para configurar o seu diretório do Azure AD e importar/exportar dados.

## <a name="errors-in-the-wizard"></a>Erros do Assistente
O Assistente de instalação está a utilizar dois contextos de segurança diferente. Na página **ligar ao Azure AD** que está a utilizar o utilizador atualmente com a sessão iniciado no. Na página **Configurar** -está a mudar para a [conta a executar o serviço para o motor de sincronização](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts). As configurações de proxy oferecemos são globais para o computador para que se existir um problema, o problema irá a maioria provavelmente já aparecem na página **ligar ao Azure AD** no assistente.

Estes são os erros mais comuns que verá no Assistente de instalação.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>O Assistente de instalação não tenha sido configurado corretamente
Este erro é apresentada quando o assistente próprio não é possível alcançar o proxy.
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

- Se vir esta situação, verifique se que o [Machine. config](active-directory-aadconnect-prerequisites.md#connectivity) foi configurado corretamente.
- Se que parecer correta, siga os passos na [conectividade de proxy de verificar](#verify-proxy-connectivity) se o problema está apresentar fora do assistente, assim.

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Não é possível alcançar o ponto final MFA
Este erro será apresentado se não é possível alcançar o ponto final **https://secure.aadcdn.microsoftonline-p.com** e o seu administrador global tem MFA ativado.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

- Se vir esta situação, certifique-se de que foi adicionado o ponto final secure.aadcdn.microsoftonline-p.com para o proxy.

### <a name="the-password-cannot-be-verified"></a>A palavra-passe não pode ser verificada
Se o Assistente de instalação for bem sucedido no ligar-se ao Azure AD, mas a própria palavra-passe não pode ser verificada, irá ver isto: ![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

- É a palavra-passe uma palavra-passe temporária e têm de ser alteradas? É realmente a palavra-passe correta? Tente iniciar sessão no https://login.microsoftonline.com (no outro computador que o servidor de ligação do Azure AD) e verifique se que a conta é utilizável.

### <a name="verify-proxy-connectivity"></a>Verificar a conectividade de proxy
Para verificar se o servidor de ligação do Azure AD tiver conectividade real com o servidor Proxy e Internet irá utilizamos algumas PowerShell para ver se o proxy é permitir pedidos web ou não. Na linha de comandos do PowerShell, execute `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tecnicamente a primeira chamada é https://login.microsoftonline.com e isto irá funcionar bem, mas o outras URI é mais rápido responder.)

PowerShell irá utilizar a configuração no Machine. config para o proxy de contacto. As definições no winhttp/netsh não devem ter impacto estes cmdlets.

Se o proxy está configurado corretamente, receberá um Estado de sucesso: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Se receber **não é possível ligar ao servidor remoto** , em seguida, PowerShell está a tentar efetuar uma chamada direta sem utilizar o proxy ou DNS não está configurado corretamente. Certificar-se de que o ficheiro **Machine. config** está configurado corretamente.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Se o proxy não está configurado corretamente, recomendamos irá obter um erro: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

Erro | Texto do erro | Comentário
---- | ---- | ---- |
403 | Proibido | Não foi aberto o proxy para o URL pedido. Revisitar a configuração do proxy e certifique-se dos [URLs](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) foram abertos.
407 | Autenticação de proxy necessária | O servidor proxy necessário iniciar sessão e não foi fornecido nenhum. Se o servidor de proxy requer autenticação, certifique-se para que isto configurado na Machine. config. Certifique-se também que estiver a utilizar contas de domínio para o utilizador a executar o assistente, bem como para a conta de serviço.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>O padrão de comunicação entre ligação do Azure AD e Azure AD
Se tiverem seguidas todos estes passos acima e ainda não é possível ligar neste momento poderão iniciar olhar para registos de rede. Esta secção está a documentar um padrão de conectividade normal e bem sucedida. -Lo também é listagem arenques vermelho comuns que pode ser ignoradas se está a ler os registos de rede.

- Será chamadas para https://dc.services.visualstudio.com. Não é necessário para este aberto no proxy para a instalação de ser concluída com êxito e estes podem ser ignoradas.
- Verá que resolução dns irá listar os anfitriões a estar a nsatc.net de espaço de nome DNS e outros espaços de nomes não em microsoftonline.com reais. No entanto, não haverá qualquer pedidos de serviço web nos nomes de servidor real e não tem de adicioná-los para o proxy.
- Os pontos finais adminwebservice e provisioningapi (consulte abaixo nos registos do) são pontos finais de deteção e utilizados para localizar o ponto final real para utilizar e será apontado dependendo da sua região.

### <a name="reference-proxy-logs"></a>Registos de proxy de referência
Eis uma cópia de um registo de proxy real e a página do Assistente de instalação a partir de onde foi tomada (entradas duplicadas para o ponto final mesmo foram removidas). Isto pode ser utilizado como uma referência para os seus próprios registos de proxy e de rede. Os pontos finais reais poderão ser diferentes no seu ambiente (em especial das *itálico*).

**Ligar ao Azure AD**

Tempo | URL
--- | ---
11/1/2016 8:31 | Connect://login.microsoftonline.com:443
11/1/2016 8:31 | Connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:32 | ligar: / /*bba800 âncora*. microsoftonline.com:443
11/1/2016 8:32 | Connect://login.microsoftonline.com:443
11/1/2016 8:33 | Connect://provisioningapi.microsoftonline.com:443
11/1/2016 8:33 | ligar: / /*bwsc02 reencaminhamento*. microsoftonline.com:443

**Configurar**

Tempo | URL
--- | ---
11/1/2016 8:43 | Connect://login.microsoftonline.com:443
11/1/2016 8:43 | ligar: / /*bba800 âncora*. microsoftonline.com:443
11/1/2016 8:43 | Connect://login.microsoftonline.com:443
11/1/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:44 | ligar: / /*bba900 âncora*. microsoftonline.com:443
11/1/2016 8:44 | Connect://login.microsoftonline.com:443
11/1/2016 8:44 | Connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:44 | ligar: / /*bba800 âncora*. microsoftonline.com:443
11/1/2016 8:44 | Connect://login.microsoftonline.com:443
11/1/2016 8:46 | Connect://provisioningapi.microsoftonline.com:443
11/1/2016 8:46 | ligar: / /*bwsc02 reencaminhamento*. microsoftonline.com:443

**Sincronização inicial**

Tempo | URL
--- | ---
11/1/2016 8:48 | Connect://login.Windows.NET:443
11/1/2016 8:49 | Connect://adminwebservice.microsoftonline.com:443
11/1/2016 8:49 | ligar: / /*bba900 âncora*. microsoftonline.com:443
11/1/2016 8:49 | ligar: / /*bba800 âncora*. microsoftonline.com:443

## <a name="authentication-errors"></a>Erros de autenticação
Esta secção abrange erros que podem ser devolvidos de ADAL (a biblioteca de autenticação utilizada pelo ligação do Azure AD) e PowerShell. O erro explicado deverá ajudá-lo, no, compreender os seus próximos passos.

### <a name="invalid-grant"></a>Conceder inválido
Nome de utilizador inválido ou palavra-passe. Para mais informações, consulte [a palavra-passe não pode ser verificada](#the-password-cannot-be-verified) .

### <a name="unknown-user-type"></a>Tipo de utilizador desconhecida
Diretório da sua Azure AD não pode ser encontrado ou resolvido. Talvez tentar iniciar sessão com um nome de utilizador num domínio não verificado?

### <a name="user-realm-discovery-failed"></a>Utilizador Realm deteção falhou
Problemas de configuração de rede ou de proxy. A rede não pode ser atingido, consulte o artigo [resolução de problemas de conectividade no Assistente de instalação](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Palavra-passe de utilizador expirou
As suas credenciais expiraram. Altere a palavra-passe.

### <a name="authorizationfailure"></a>AuthorizationFailure
Problema desconhecido.

### <a name="authentication-cancelled"></a>Autenticação cancelada
O desafio autenticação multifator (MFA) foi cancelado.

### <a name="connecttomsonline"></a>ConnectToMSOnline
Autenticação foi efetuada com êxito, mas Azure AD PowerShell tem um problema de autenticação.

### <a name="azurerolemissing"></a>AzureRoleMissing
Autenticação foi efetuada com êxito. Não é um administrador global.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
Autenticação foi efetuada com êxito. Gestão de identidades privilegiados ter sido ativada e atualmente não for um administrador global. Para mais informações, consulte [Gestão de identidades privilegiados](active-directory-privileged-identity-management-getting-started.md) .

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
Autenticação foi efetuada com êxito. Não foi possível obter informações da empresa a partir do Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
Autenticação foi efetuada com êxito. Não foi possível obter informações sobre o domínio a partir do Azure AD.

## <a name="troubleshooting-steps-for-previous-releases"></a>Passos de resolução de problemas para versões anteriores.
Com as versões começa com o número de compilação 1.1.105.0 (disponibilizado Fevereiro de 2016) foi foi removido o Assistente de início de sessão. Esta secção e a configuração já não devem ser necessários, mas é mantido como referência.

Para o sessão único no Assistente para trabalhar, tem de ser configurado winhttp. Isto pode ser feito com [**netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>O Assistente de início de sessão não tenha sido configurado corretamente
Este erro aparecer quando o Assistente de início de sessão não é possível alcançar o proxy ou o proxy não permitindo que o pedido.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

- Se vir esta situação, observe a configuração do proxy em [netsh](active-directory-aadconnect-prerequisites.md#connectivity) e verifique se estiver correto.
![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
- Se que parecer correta, siga os passos na [conectividade de proxy de verificar](#verify-proxy-connectivity) se o problema está apresentar fora do assistente, assim.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
