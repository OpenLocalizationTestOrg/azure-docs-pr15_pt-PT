<properties
    pageTitle="Configurar o registo automático de dispositivos com Windows façam parte de um domínio com o Azure Active Directory | Microsoft Azure"
    description="Configure o seus dispositivos com Windows façam parte de um domínio para registar automaticamente e em silêncio com o Azure Active Directory."
    services="active-directory"
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
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configurar o registo automático do Windows dispositivos façam parte de um domínio com o Azure Active Directory

Para utilizar o [Azure Active Directory access de condicional com base no dispositivo](active-directory-conditional-access.md), tem de estar registados os computadores de domínio do Windows com o Azure Active Directory (Azure AD). Neste artigo, pode obter o que precisa de fazer para configurar o registo de domínio os dispositivos Windows com o Azure AD na sua organização.

Utilizar o access condicional no Azure AD dá-lhe estas vantagens:

-   Experiência melhorado sessão único (SSO) nas aplicações do Azure AD através das contas escolar ou profissional
-   Enterprise roaming das definições em todos os dispositivos
-   Acesso da loja Windows para empresas
-   Autenticação mais forte e conveniente iniciar sessão com Olá do Windows

> [AZURE.NOTE] A atualização do Windows 10 Novembro oferece alguns das experiências do utilizador melhorada no Azure AD, mas a atualização do Windows 10 aniversário suporta totalmente acesso condicional com base no dispositivo. Para mais informações sobre acesso condicional, consulte o artigo [Azure Active Directory acesso de condicional com base no dispositivo](active-directory-conditional-access.md). Para obter mais informações sobre os dispositivos Windows 10, no local de trabalho e como um utilizador regista o um dispositivo Windows 10 com o Azure AD, consulte o artigo [Windows 10 para a empresa: utilizar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md).

Pode registar algumas versões anteriores do Windows, incluindo estas versões:

-   Windows 8.1
-   Windows 7

Se estiver a utilizar um computador Windows Server como um ambiente de trabalho, pode registar estas plataformas:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>Pré-requisitos

O principal requisito de registo automático de domínio dispositivos utilizando o Azure AD deve ter uma versão atualizada do Azure Active Directory estabelecer ligação (ligação do Azure AD).

Dependendo da forma como implementou ligação do Azure AD e se tiver utilizado uma instalação personalizada ou express ou uma atualização no local, os pré-requisitos seguintes poderão ter sido configurados automaticamente:

-   **Ponto de ligação de serviço no Active Directory local**. Para deteção de informações de inquilino do Azure AD por computadores que registe-se no Azure AD.

-   **Serviços de Federação do active Directory (AD FS) emissão transformar regras**. Para a autenticação do computador no registo (aplicável às configurações federadas).

Se alguns dispositivos no seu organizações não estiverem dispositivos de domínio do Windows 10, certifique-se de que efetuar as seguintes tarefas:

- Definir uma política de no Azure AD para que os utilizadores podem registar dispositivos
- Configurar autenticação de Windows integrada (IWA) como uma alternativa válida para autenticação multifator no AD FS



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>Definir um ponto de ligação de serviço para deteção do inquilino Azure AD

Tem de existir um objeto de ponto de ligação do serviço na configuração de nomenclatura partição de contexto da floresta do domínio onde são associadas computadores. O ponto de ligação de serviço de suspensões deteção obter informações sobre o inquilino do Azure AD onde registam computadores. Numa configuração do Active Directory floresta múltipla, o ponto de ligação de serviço tem de existir em florestas todos os que têm computadores façam parte de um domínio.

Defina o ponto de ligação de serviço nestas localizações no Active Directory:

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] Para uma floresta com o Active Directory domínio nome *exemplo.com*, a configuração de contexto de nomenclatura é CN = Configuration, Cc = exemplo, CC = com.

Pode verificar os valores de objeto e deteção de inquilino do Azure AD utilizando o seguinte script do Windows PowerShell. (Substitua o contexto de nomenclatura de configuração no exemplo com o seu contexto de nomenclatura de configuração.)

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

O `$scp.Keywords` saída mostra as informações de inquilino do Azure AD:

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se o ponto de ligação de serviço não existir, criá-lo executando o seguinte script do PowerShell no servidor de ligação do Azure AD:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]Quando executa `$aadAdminCred = Get-Credential`, utilize o formato *user@example.com* para o nome de utilizador na caixa de diálogo **Obter credenciais** .  
> Quando executa o cmdlet inicialização ADSyncDomainJoinedComputerSync, substitua [*nome da conta conexão*] a conta do domínio que é utilizada na conta de conector do Active Directory.  
> O cmdlet utiliza o módulo do Active Directory PowerShell, que depende de serviços de Web do Active Directory num controlador de domínio. Serviços de Web do Active Directory é suportado no controladores de domínio no Windows Server 2008 R2 e versões posteriores. Controladores de domínio no Windows Server 2008 ou versões anteriores, utilizar a API System. DirectoryServices através do PowerShell para criar o ponto de ligação de serviço e, em seguida, atribua os valores de palavras-chave.

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>Criar regras de AD FS para o registo de dispositivo instantâneas em organizações federadas

Num Azure federado configuração AD, dispositivos confiar AD FS (ou no servidor de Federação no local) para autenticar Azure AD. Em seguida, registam contra Azure Active Directory dispositivo registo (serviço Azure AD dispositivo registo).

> [AZURE.NOTE] Numa configuração não federado, hashes de palavra-passe de utilizador são sincronizadas para Azure AD e computadores com Windows 10 e Windows Server 2016 façam parte de um domínio autenticar serviço de registo do Azure AD dispositivo. Um utilizador autentica utilizando uma credencial que o utilizador escreve para as respetivas contas do computador no local, e que é reencaminhado para Azure AD através da ligação do Azure AD. Para que não sejam do Windows 10 e Windows Server 2016 computadores numa configuração não federado, tem opções para configurar uma dispositivo com base em autoridade de certificação na sua organização. Para obter mais informações, consulte os pacotes de transferir o Windows Installer de secção para o Windows de 10 computadores neste artigo.

Para computadores com Windows 10 e Windows Server 2016, ligação do Azure AD associa o objeto de dispositivo no Azure AD o objeto de conta de computador no local. As seguintes afirmações tem de existir previamente durante a autenticação do serviço de registo de dispositivo do Azure AD concluir o registo e criar o objeto de dispositivo:

- http://schemas.microsoft.com/ws/2012/01/AccountType contém o valor de DJ, que identifica o autenticador principal como um computador associado ao domínio.
- http://schemas.microsoft.com/Identity/Claims/onpremobjectguid contém o valor do atributo **GUID de objecto** da conta de computador no local.
- http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarysid contém identificador de segurança principal do computador (SID), que corresponde ao valor do atributo **objectSid** da conta de computador no local.
- http://schemas.microsoft.com/ws/2008/06/Identity/Claims/issuerid contém o valor que utiliza o Azure AD para confiar o token emitido a partir do AD FS ou a partir da no local segurança Token serviço (STS). Isto é importante numa configuração do Active Directory floresta múltipla. Nesta configuração, poderão ser associadas a uma floresta que não seja aquele que liga-se ao Azure AD no AD FS ou no local STS computadores. Para o AD FS, utilize o valor http://<*nome de domínio*>/adfs/serviços/fidedignidade /, onde <*nome de domínio*> é o nome de domínio validado no Azure AD.

Para criar estas regras manualmente, no AD FS, utilize o seguinte script do PowerShell numa sessão de que está ligada ao seu servidor. Substitua a primeira linha com o seu nome de domínio validado organização no Azure AD.

> [AZURE.NOTE] Se não utiliza o AD FS para o seu servidor de Federação no local, siga instruções do seu fornecedor para criar as regras que emitir destas afirmações.

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] Se o seu ambiente no local for uma única floresta, utilize as primeiras três regras. Se os computadores estão numa floresta diferente daquele sincronizar com o Azure AD ou se o utilizar nomes alternativos para aqueles na configuração de sincronização, também tem de incluir as regras restantes.

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] Computadores com Windows 10 e Windows Server 2016 associarem ao domínio auto-autentiquem utilizando IWA para um extremo WS fidedignidade ativo que está alojado pelo AD FS. Certifique-se de que o ponto final está ativo. Se estiver a utilizar o Proxy de aplicação Web, certifique-se de que este ponto final é publicado através de um proxy. O ponto final é adfs/serviços/fidedignidade/13/windowstransport. Para verificar se o mesmo está ativo, na consola de gestão do AD FS, aceda ao **serviço** > **pontos finais**. Se não utiliza o AD FS para o seu servidor de Federação no local, siga as instruções do seu fornecedor para se certificar de que o ponto final correspondente está ativo.



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>Configurar o AD FS para a autenticação do registo de dispositivo

Certifique-se de que IWA está definido como uma alternativa válida para autenticação multifator para o registo de dispositivo no AD FS. Para fazer isto, tem de ter uma emissão transformar a regra que atravessa o método de autenticação.

1. Na consola de gestão do AD FS, vá para **o AD FS** > **Relações de fidedignidade** > **Confiar fidedignidades de terceiros**.

2. Botão direito do rato no objeto de confiança de festa dependente da plataforma de identidade do Microsoft Office 365 e, em seguida, selecione **Editar regras afirmação**.

3.  No separador **Regras de transformação de emissão** , selecione **Adicionar regra**.

4.  Na lista de modelos de **regra afirmação** , selecione **Enviar em afirmações através de uma regra personalizada**.

5.  Selecione **seguinte**.

6.  Na caixa **nome da regra afirmação** , introduza **Auth método afirmação regra**.

7.  Na caixa da **regra afirmação** , introduza este comando:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. No seu servidor de Federação, introduza este comando do PowerShell:

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> é o nome do objeto festa dependente do Azure AD dependente festa fidedignidade objeto. Este objeto normalmente chama-se *Plataforma de identidade do Microsoft Office 365*.



## <a name="deployment-and-rollout"></a>Implementação e de expansão

Quando computadores associados ao domínio cumpram os pré-requisitos, está prontos para registar Azure AD.

Os computadores de domínio actualização de aniversário do Windows 10 e Windows Server 2016 registe-se automaticamente com o Azure AD da próxima vez que o dispositivo reinicia ou quando um utilizador inicia sessão no Windows. Novos computadores que são associados ao domínio registe-se com o Azure AD quando o dispositivo for reiniciado após a operação de associação de domínio.

> [AZURE.NOTE] Computadores de domínio do Windows 10 automaticamente registe-se com o Azure AD apenas se o objeto de política de grupo de implementação estiver definido.

Pode utilizar um objeto de política de grupo para controlar a implementação de registo automática do Windows 10 e Windows Server 2016 associarem ao domínio computadores. Para implementar o registo automático de computadores de domínio que não sejam Windows 10, pode implementar um pacote de instalação do Windows para computadores que selecionar.

> [AZURE.NOTE] A política de grupo para o controlo de implementação accionadores também o registo de computadores façam parte de um domínio do Windows 8.1. Pode utilizar a política para registar os computadores de domínio do Windows 8.1. Em alternativa, se tiver uma mistura de versões do Windows, incluindo as versões do Windows 7 ou Windows Server, pode registar o Windows de 10 e Windows Server 2016 computadores ao utilizar um pacote de instalação do Windows.

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Criar um objeto de política de grupo para controlar a implementação do registo automática

Para controlar a implementação do registo automático de computadores façam parte de um domínio com o Azure AD, pode implementar a política de grupo **Registar computadores façam parte de um domínio como dispositivos** para os computadores que deseja registar. Por exemplo, pode implementar a política para uma unidade organizacional ou para um grupo de segurança.

Para definir a política, efetue os seguintes passos:

1. Abra o Gestor de servidor e, em seguida, aceda a **Ferramentas** > **Gestão da política de grupo**.

2. Vá para o nó do domínio que corresponde ao domínio em que pretende ativar registo automático de computadores Windows 10 ou Windows Server 2016.

3. **Objectos de política de grupo**com o botão direito e, em seguida, selecione **Novo**.

4. Introduza um nome para o objeto de política de grupo. Por exemplo, o *Registo automático para Azure AD*. Clique **em OK**.

4. O novo objeto de política de grupo com o botão direito e, em seguida, selecione **Editar**.

5. Aceda a **configuração do computador** > **políticas** > **Modelos administrativos** > **componentes do Windows** > **registo de dispositivo**. Com o botão direito **Register domínio associadas computadores como dispositivos**e, em seguida, selecione **Editar**.

    > [AZURE.NOTE] Este modelo de política de grupo ter mudado de versões anteriores da consola de gestão de política de grupo. Se estiver a utilizar uma versão anterior da consola, aceda a **Configuração do computador** > **políticas** > **Modelos administrativos** > **Componentes do Windows** > **Associação do local de trabalho** > **automaticamente local de trabalho associação computadores cliente**.

6. Selecione **ativada**e, em seguida, selecione **Aplicar**.

7. Clique **em OK**.

8. Ligar o objeto de política de grupo a uma localização da sua escolha. Por exemplo, pode ligá-lo para uma unidade organizacional específica. Também pode ligar a um grupo de segurança específica de computadores automaticamente registar Azure AD. Para definir esta política para todos os façam parte de um domínio Windows 10 e Windows Server 2016 computadores na sua organização, ligar o objeto de política de grupo para o domínio.

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>Transferir pacotes do Windows Installer para computadores que não sejam Windows 10  

Para registar computadores façam parte de um domínio com o Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, pode transferir e instalar estes ficheiros de pacote. (MSI) do Windows Installer:

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Implemente o pacote utilizando um sistema de distribuição de software como o Gestor de configuração do Centro de sistema. O pacote suporta as opções de instalação automática padrão com o parâmetro *sossegada* . Gestor de configuração do sistema Centro 2016 oferece benefícios adicionais de versões anteriores, como a capacidade de controlar registos concluídos. Para mais informações, consulte o artigo [sistema Centro de 2016](https://www.microsoft.com/en-us/cloud-platform/system-center)

O programa de instalação cria uma tarefa agendada no sistema que é executada no contexto do utilizador. A tarefa é acionada quando o utilizador inicia sessão Windows. A tarefa em silêncio regista o dispositivo com Azure AD com as credenciais de utilizador depois de autenticar através de IWA. Para ver a tarefa agendada, vá para a **Microsoft** > **Participar local de trabalho**e, em seguida, aceda à biblioteca do Programador de tarefas.



## <a name="next-steps"></a>Próximos passos

- [Acesso ao condicional Azure Active Directory](active-directory-conditional-access.md)
