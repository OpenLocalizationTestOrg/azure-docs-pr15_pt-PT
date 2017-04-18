<properties
   pageTitle="Criar Azure service principal com PowerShell | Microsoft Azure"
   description="Descreve como utilizar o PowerShell do Azure para criar uma aplicação do Active Directory e principal do serviço e conceder-lhe acesso aos recursos através de controlo de acesso baseado em funções. Mostra como autenticar aplicação com uma palavra-passe ou um certificado."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Utilizar o PowerShell do Azure para criar um principal de serviço para aceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Clip Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)

Quando tiver uma aplicação ou script que precisa de aceder a recursos, provavelmente não pretende executar este processo sob as suas credenciais. Poderá ter permissões diferentes que pretende para a aplicação e não pretende que a aplicação para continuar com as suas credenciais se alterar as suas responsabilidades. Em vez disso, cria uma identidade para a aplicação que inclui as credenciais de autenticação e atribuições de funções. Sempre que a aplicação é executado, autentica próprio com estas credenciais. Este tópico mostra-lhe como utilizar o [Azure PowerShell](powershell-install-configure.md) para configurar o tudo o que precisa para uma aplicação para ser executada nas suas próprias credenciais e de identidade.

Com o PowerShell, tem duas opções para autenticar a sua aplicação de AD:

 - palavra-passe
 - certificado

Este tópico mostra como utilizar ambas as opções no PowerShell. Se pretender iniciar sessão no Azure a partir de um quadro de programação (essas Python, Rubi ou Node.js), autenticação de palavra-passe poderá a melhor opção. Antes de decidir se deve utilizar uma palavra-passe ou o certificado, consulte a secção [aplicações de exemplo](#sample-applications) para obter exemplos sobre os quadros diferentes de autenticação.

## <a name="active-directory-concepts"></a>Conceitos do activos Directory

Neste artigo, crie dois objetos - a aplicação do Active Directory (AD) e o serviço do principal. A aplicação de AD é a representação global da sua aplicação. Contém as credenciais (um id da aplicação e uma palavra-passe ou certificado). O serviço principal está a representação da sua aplicação de um Active Directory local. Contém a atribuição de funções. Este tópico foca-se numa única inquilino aplicação onde a aplicação destina-se para executar na apenas uma organização. Utilizar normalmente único inquilino aplicações para aplicações de linha de negócio que são executadas dentro da sua organização. Numa aplicação único inquilino, tem uma aplicação de AD e principal de um serviço.

Poderá estar a pensar - por que motivo preciso ambos os objectos? Esta abordagem torna mais sentido quando considerar inquilinos com várias aplicações. É normalmente utilizado inquilinos com várias aplicações para as aplicações do software-como-a-service (SaaS), onde a aplicação é executado em muitos subscrições diferentes. Para as aplicações com várias inquilinos, tem uma aplicação de AD e vários principais de serviço (um em cada Active Directory que concede acesso à aplicação). Para configurar uma aplicação do inquilino com várias, consulte o artigo [Guia do programador autorização com a API do Gestor de recursos do Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permissões necessárias

Para concluir este tópico, tem de ter permissões suficientes na sua Azure Active Directory e a sua subscrição Azure. Especificamente, tem de conseguir criar uma aplicação no Active Directory e atribuir o capital de serviço a uma função. 

No seu Active Directory, sua conta tem de ser um administrador (como **Administrador Global** ou **Administrador de utilizadores**). Se a sua conta é atribuída a função de **utilizador** , tem de ter um elevar as permissões de administrador.

Na sua subscrição, sua conta tem de ter `Microsoft.Authorization/*/Write` acesso, o que é concedido através da função de [proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou a função de [Administrador de acesso do utilizador](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Se a sua conta é atribuída à função **Contribuinte** , receber um erro ao tentar atribuir o capital de serviço a uma função. Novamente, o administrador de subscrição tem de lhe conceder acesso suficiente.

Agora, avance para uma secção para a [palavra-passe](#create-service-principal-with-password) ou [ao certificado de](#create-service-principal-with-certificate) autenticação.

## <a name="create-service-principal-with-password"></a>Criar o serviço principal com palavra-passe

Nesta secção, execute os passos para:

- criar a aplicação de AD com uma palavra-passe
- criar o capital de serviço
- atribuir uma função de leitor para o capital de serviço

Para efetuar rapidamente estes passos, consulte os seguintes três cmdlets. 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Vejamos estes passos mais atentamente para se certificar de que a compreender o processo.

1. Inicie sessão sua conta.

        Add-AzureRmAccount

1. Crie uma nova aplicação do Active Directory ao fornecer um nome a apresentar, o URI que descreva a sua aplicação, o URIs que identificam a aplicação e a palavra-passe para a sua identidade da aplicação.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Para as aplicações de inquilino único, a URIs não são validados.
     
     Se a sua conta não possui as [permissões necessárias](#required-permissions) no Active Directory, verá uma mensagem de erro que indica "Authentication_Unauthorized" ou "nenhuma subscrição encontrada no contexto".

1. Examine novo objecto de aplicação. 

        $app
        
     Nota: em particular a propriedade de **ApplicationId** , que é necessária para criar principais de serviço, atribuições de funções e adquirir o token de acesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Crie um principal para a sua aplicação de serviço ao passagem no id da aplicação da aplicação do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Conceda as permissões do principal de serviço na sua subscrição. Neste exemplo, adicione o capital de serviço para a função de **leitor** , o que concede permissão de leitura todos os recursos na subscrição. Para outras funções, consulte o artigo [RBAC: funções incorporadas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , fornece **ApplicationId** que utilizou quando criar a aplicação. 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se a sua conta não possui permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem indicar a sua conta **não tem autorização para executar a ação Microsoft.Authorization/roleAssignments/write ao longo do âmbito ' / subscrições / {guid}'**. 

Já está! A aplicação de AD e os principais de serviço estão configurados. A secção seguinte mostra como iniciar sessão com a credencial através do PowerShell. Se pretender utilizar a credencial na sua aplicação de código, pode ir para as [aplicações de exemplo](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Fornecer credenciais através do PowerShell

Agora, tem de iniciar sessão como a aplicação para efetuar operações.

1. Crie um objecto **PSCredential** que contém as suas credenciais ao executar o comando **Get-credenciais** . É necessário o **ApplicationId** antes de executar este comando por isso, certifique-se de que estão disponíveis para colar.

        $creds = Get-Credential

2. Lhe for pedido que introduza as suas credenciais. Para o nome de utilizador, utilize **ApplicationId** que utilizou quando criar a aplicação. A palavra-passe, use o que especificou quando criar a conta.

     ![introduzir as credenciais](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Sempre que iniciar sessão como um principal de serviço, tem de fornecer o id do inquilino do diretório da sua aplicação de AD. Um inquilino é uma instância do Active Directory. Se apenas tiver uma subscrição, pode utilizar:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Se tiver mais do que uma subscrição, especifique a subscrição onde reside o Active Directory. Para mais informações, consulte o artigo [como Azure subscrições estão associadas a Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Inicie sessão como o capital de serviço ao especificar que esta conta é um principal de serviço e ao fornecer o objeto de credenciais. 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     Agora são autenticados como o serviço principal para a aplicação do Active Directory que criou.

### <a name="save-access-token-to-simplify-log-in"></a>Guardar token de acesso para simplificar o início de sessão

Para evitar fornecer as credenciais principal do serviço sempre que necessita para iniciar sessão, pode guardar o token de acesso.

1. Para utilizar o token de acesso atual numa sessão de posterior, guarde o perfil.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Abra o perfil e analisar os seus conteúdos. Repare que contém um token de acesso. 
        
2. Em vez de iniciar a sessão manualmente novamente, basta carrega o perfil.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] O token de acesso expira, por isso utilizar um perfil guardado apenas funciona para, desde que o token é válido.
        
## <a name="create-service-principal-with-certificate"></a>Criar o serviço principal com certificado

Nesta secção, execute os passos para:

- criar um certificado autoassinado
- criar a aplicação de AD com o certificado
- criar o capital de serviço
- atribuir uma função de leitor para o capital de serviço

Para efetuar rapidamente estes passos com Azure PowerShell 2.0 no Windows 10 ou Windows Server 2016 pré-visualização técnica, consulte os seguintes cmdlets. 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Vejamos estes passos mais atentamente para se certificar de que a compreender o processo. Este artigo também mostra como realizar as tarefas ao utilizar versões anteriores do Azure PowerShell ou os sistemas operativos.

### <a name="create-the-self-signed-certificate"></a>Criar o certificado autoassinado

A versão do PowerShell disponível com o Windows 10 e Windows Server 2016 pré-visualização técnica tem um cmdlet **Novo SelfSignedCertificate** atualizado para gerar um certificado autoassinado. Sistemas operativos anteriores têm o cmdlet novo SelfSignedCertificate mas não oferecer os parâmetros necessários para neste tópico. Em vez disso, tem de importar um módulo para gerar o certificado. Este tópico mostra as duas abordagens para gerar o certificado com base no sistema operativo que tiver. 

- Se tiver o **Windows 10 ou Windows Server 2016 pré-visualização técnica**, execute o seguinte comando para criar um certificado autoassinado: 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Se **não possui o Windows 10 ou Windows Server 2016 pré-visualização técnica**, tem de transferir [personalizada iniciou sessão gerador de certificado](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) a partir do Microsoft Script Center. Extrair os seus conteúdos e importe o cmdlet que precisar.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Em seguida, gere o certificado.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Possui o seu certificado e pode prosseguir com a sua aplicação de AD de criação.

### <a name="create-the-active-directory-app-and-service-principal"></a>Criar a aplicação do Active Directory e o serviço principal

1. Obter o valor de chave do certificado.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Inicie sessão sua conta Azure.

        Add-AzureRmAccount

3. Crie uma nova aplicação do Active Directory ao fornecer um nome a apresentar, o URI que descreva a sua aplicação, o URIs que identificam a aplicação e a palavra-passe para a sua identidade da aplicação.

     Se tiver Azure PowerShell 2.0 utilização (Agosto 2016 ou posterior), o cmdlet seguinte:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Se tiver Azure PowerShell 1.0, utilize o cmdlet seguinte:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Para as aplicações de inquilino único, a URIs não são validados.
    
    Se a sua conta não possui as [permissões necessárias](#required-permissions) no Active Directory, verá uma mensagem de erro que indica "Authentication_Unauthorized" ou "nenhuma subscrição encontrada no contexto".
        
    Examine novo objecto de aplicação. 

        $app

    Repare que a propriedade de **ApplicationId** , que é necessária para criar principais de serviço, atribuições de funções e adquirir tokens de acesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Crie um principal para a sua aplicação de serviço ao passagem no id da aplicação da aplicação do Active Directory.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Conceda as permissões do principal de serviço na sua subscrição. Neste exemplo, adicione o capital de serviço para a função de **leitor** , o que concede permissão de leitura todos os recursos na subscrição. Para outras funções, consulte o artigo [RBAC: funções incorporadas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , fornece **ApplicationId** que utilizou quando criar a aplicação.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se a sua conta não possui permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem indicar a sua conta **não tem autorização para executar a ação Microsoft.Authorization/roleAssignments/write ao longo do âmbito ' / subscrições / {guid}'**.

Já está! A aplicação de AD e os principais de serviço estão configurados. A secção seguinte mostra como iniciar sessão com o certificado através do PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornecer certificado através de script do PowerShell automatizado

Sempre que iniciar sessão como um principal de serviço, tem de fornecer o id do inquilino do diretório da sua aplicação de AD. Um inquilino é uma instância do Active Directory. Se apenas tiver uma subscrição, pode utilizar:

    $tenant = (Get-AzureRmSubscription).TenantId
    
Se tiver mais do que uma subscrição, especifique a subscrição onde reside o Active Directory. Para obter mais informações, consulte [administrar no diretório da sua Azure AD](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Para autenticar no seu script, especifique a conta é um serviço principal e forneça a impressão digital do certificado, o id da aplicação e o id do inquilino. Para automatizar o script, pode armazenar estes valores como variáveis de ambiente e obtê-las durante a execução ou pode incluí-los no seu script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

Agora são autenticados como o serviço principal para a aplicação do Active Directory que criou.

## <a name="sample-applications"></a>Aplicações de exemplo

As seguintes aplicações de exemplo mostram como iniciar sessão como o capital de serviço.

**.NET**

- [Implementar uma SSH ativado VM com um modelo com o .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gerir grupos de recursos com o .NET e recursos Azure](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Introdução com recursos - implementar a utilizar o modelo do Gestor de recursos do Azure - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Introdução com recursos - gerir o grupo de recursos - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Implementar uma SSH ativado VM com um modelo no Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestão de recursos Azure e grupos de recursos com Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**NODE.js**

- [Implementar uma SSH ativado VM com um modelo no Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gerir grupos de recursos com Node.js e recursos Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Rubi**

- [Implementar uma SSH ativado VM com um modelo no Rubi](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestão de recursos Azure e grupos de recursos com Rubi](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Próximos passos
  
- Para obter passos detalhados sobre integrar uma aplicação do Azure para gestão de recursos, consulte o [Guia do programador autorização com a API do Gestor de recursos do Azure](resource-manager-api-authentication.md).
- Para obter uma explicação mais detalhada das aplicações e principais de serviço, consulte o artigo [aplicação objectos e Principal de serviço](./active-directory/active-directory-application-objects.md). 
- Para mais informações sobre a autenticação do Active Directory, consulte o artigo [Cenários de autenticação de Azure AD](./active-directory/active-directory-authentication-scenarios.md).



