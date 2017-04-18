<properties
   pageTitle="Criar o serviço principal com Azure clip | Microsoft Azure"
   description="Descreve como utilizar o clip do Azure para criar uma aplicação do Active Directory e principal do serviço e conceder-lhe acesso aos recursos através de controlo de acesso baseado em funções. Mostra como autenticar aplicação com uma palavra-passe ou um certificado."
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
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Utilizar o clip do Azure para criar um principal de serviço para aceder a recursos

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Clip Azure](resource-group-authenticate-service-principal-cli.md)
- [Portal](resource-group-create-service-principal-portal.md)


Quando tiver uma aplicação ou script que precisa de aceder a recursos, provavelmente não pretende executar este processo sob as suas credenciais. Poderá ter permissões diferentes que pretende para a aplicação e não pretende que a aplicação para continuar com as suas credenciais se alterar as suas responsabilidades. Em vez disso, cria uma identidade para a aplicação que inclui as credenciais de autenticação e atribuições de funções. Sempre que a aplicação é executado, autentica próprio com estas credenciais. Este tópico mostra-lhe como utilizar o [Clip do Azure para Mac, Linux e Windows](xplat-cli-install.md) para configurar uma aplicação para ser executada nas suas próprias credenciais e de identidade.

Com o clip do Azure, tem duas opções para autenticar a sua aplicação de AD:

 - palavra-passe
 - certificado

Este tópico mostra como utilizar ambas as opções no Azure clip. Se pretender iniciar sessão no Azure a partir de um quadro de programação (essas Python, Rubi ou Node.js), autenticação de palavra-passe poderá a melhor opção. Antes de decidir se deve utilizar uma palavra-passe ou o certificado, consulte a secção [aplicações de exemplo](#sample-applications) para obter exemplos sobre os quadros diferentes de autenticação.

## <a name="active-directory-concepts"></a>Conceitos do activos Directory

Neste artigo, crie dois objetos - a aplicação do Active Directory (AD) e o serviço do principal. A aplicação de AD é a representação global da sua aplicação. Contém as credenciais (um id da aplicação e uma palavra-passe ou certificado). O serviço principal está a representação da sua aplicação de um Active Directory local. Contém a atribuição de funções. Este tópico foca-se numa única inquilino aplicação onde a aplicação destina-se para executar na apenas uma organização. Utilizar normalmente único inquilino aplicações para aplicações de linha de negócio que são executadas dentro da sua organização. Numa aplicação único inquilino, tem uma aplicação de AD e principal de um serviço.

Poderá estar a pensar - por que motivo preciso ambos os objectos? Esta abordagem torna mais sentido quando considerar inquilinos com várias aplicações. É normalmente utilizado inquilinos com várias aplicações para as aplicações do software-como-a-service (SaaS), onde a aplicação é executado em muitos subscrições diferentes. Para as aplicações com várias inquilinos, tem uma aplicação de AD e vários principais de serviço (um em cada Active Directory que concede acesso à aplicação). Para configurar uma aplicação do inquilino com várias, consulte o artigo [Guia do programador autorização com a API do Gestor de recursos do Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Permissões necessárias

Para concluir este tópico, tem de ter permissões suficientes na sua Azure Active Directory e a sua subscrição Azure. Especificamente, tem de conseguir criar uma aplicação no Active Directory e atribuir o capital de serviço a uma função. 

No seu Active Directory, sua conta tem de ser um administrador (como **Administrador Global** ou **Administrador de utilizadores**). Se a sua conta é atribuída a função de **utilizador** , tem de ter um elevar as permissões de administrador.

Na sua subscrição, sua conta tem de ter `Microsoft.Authorization/*/Write` acesso, o que é concedido através da função de [proprietário](./active-directory/role-based-access-built-in-roles.md#owner) ou a função de [Administrador de acesso do utilizador](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Se a sua conta é atribuída à função **Contribuinte** , receber um erro ao tentar atribuir o capital de serviço a uma função. Novamente, o administrador de subscrição tem de lhe conceder acesso suficiente.

Agora, avance para uma secção para a [palavra-passe](#create-service-principal-with-password) ou [ao certificado de](#create-service-principal-with-certificate) autenticação.

## <a name="create-service-principal-with-password"></a>Criar o serviço principal com palavra-passe

Nesta secção, execute os passos para criar a aplicação de AD com uma palavra-passe e atribuir uma função de leitor para o serviço principal.

Vejamos estes passos.

1. Inicie sessão sua conta.

        azure login

1. Tem duas opções para criar a aplicação de AD. Pode criar a aplicação de AD e o serviço do principal num único passo ou criá-las em separado. Criá-los num único passo se não precisar de especificar uma home page e identificador URIs para a sua aplicação. Criá-los separadamente se precisar de definir estes valores para uma aplicação web. Ambas as opções são apresentadas neste passo.

     - Para criar a aplicação de AD e o serviço principal num único passo, fornece o nome da aplicação e uma palavra-passe, conforme o seguinte comando:
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - Para criar a aplicação de AD em separado, fornece o nome da aplicação, uma home page URI, identificador URIs e uma palavra-passe, conforme o seguinte comando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         O comando anterior devolve um valor AppId. Para criar um principal de serviço, forneça esse valor como um parâmetro no seguinte comando:
     
            azure ad sp create -a <AppId>
     
     Se a sua conta não possui as [permissões necessárias](#required-permissions) no Active Directory, verá uma mensagem de erro que indica "Authentication_Unauthorized" ou "nenhuma subscrição encontrada no contexto".
    
     Para ambas as opções, é devolvido o novo principal de serviço. O **Id do objeto** é necessária quando conceder permissões. É necessário o guid listado com os **Nomes principais de serviço** ao iniciar sessão. Este guid é o mesmo valor que o id da aplicação. As aplicações de exemplo, este valor é referido como o **ID de cliente**. 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Conceda as permissões do principal de serviço na sua subscrição. Neste exemplo, adicione o capital de serviço para a função de **leitor** , o que concede permissão de leitura todos os recursos na subscrição. Para outras funções, consulte o artigo [RBAC: funções incorporadas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , forneça o **ID de objecto** que utilizou quando criar a aplicação. 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Se a sua conta não possui permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem indicar a sua conta **não tem autorização para executar a ação Microsoft.Authorization/roleAssignments/write ao longo do âmbito ' / subscrições / {guid}'**. 

Já está! A aplicação de AD e os principais de serviço estão configurados. A secção seguinte mostra como iniciar sessão com a credencial através do clip Azure. Se pretende utilizar a credencial na sua aplicação de código, não terá continuar com neste tópico. Pode ir para as [aplicações de exemplo](#sample-applications) para obter exemplos de iniciar sessão com o seu id da aplicação e a palavra-passe. 

### <a name="provide-credentials-through-azure-cli"></a>Fornecer credenciais através do clip do Azure

Agora, tem de iniciar sessão como a aplicação para efetuar operações.

1. Sempre que iniciar sessão como um principal de serviço, tem de fornecer o id do inquilino do diretório da sua aplicação de AD. Um inquilino é uma instância do Active Directory. Para obter o id do inquilino para a sua subscrição atualmente autenticado, utilize:

        azure account show

     Qual devolve:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se o que precisa para obter o id de inquilino do outra subscrição, utilize o seguinte comando:

        azure account show -s {subscription-id}

2. Se for necessário para obter o id do cliente para utilizar para o início de sessão, utilize:

        azure ad sp show -c exampleapp --json

     O valor a utilizar para o início de sessão é o guid listado nos nomes principais de serviço.

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. Inicie sessão como o capital de serviço.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    É-lhe pedida a palavra-passe. Forneça a palavra-passe que especificou quando criar a aplicação de AD.

        info:    Executing command login
        Password: ********

Agora são autenticados como o capital de serviço do capital de serviço que criou.

## <a name="create-service-principal-with-certificate"></a>Criar o serviço principal com certificado

Nesta secção, execute os passos para:

- criar um certificado autoassinado
- criar a aplicação de AD com o certificado e o capital de serviço
- atribuir uma função de leitor para o capital de serviço

Para concluir estes passos, tem de ter [OpenSSL](http://www.openssl.org/) instalado.

1. Crie um certificado autoassinado.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combine as chaves públicas e privadas.

        cat privkey.pem cert.pem > examplecert.pem

3. Abra o ficheiro **examplecert.pem** e procure a sequência de carateres entre **-início certificado-** e **-Terminar certificado-**longa. Copie os dados de certificado. Passar estes dados como um parâmetro quando criar o serviço principal.

1. Inicie sessão sua conta.

        azure login

1. Tem duas opções para criar a aplicação de AD. Pode criar a aplicação de AD e o serviço do principal num único passo ou criá-las em separado. Criá-los num único passo se não precisar de especificar uma home page e identificador URIs para a sua aplicação. Criá-los separadamente se precisar de definir estes valores para uma aplicação web. Ambas as opções são apresentadas neste passo.

     - Para criar a aplicação de AD e o serviço principal num único passo, fornece o nome da aplicação e os dados de certificado, conforme o seguinte comando:
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - Para criar a aplicação de AD em separado, fornece o nome da aplicação, uma home page URI, identificador URIs e os dados de certificado, conforme o seguinte comando:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         O comando anterior devolve um valor AppId. Para criar um principal de serviço, forneça esse valor como um parâmetro no seguinte comando:
     
            azure ad sp create -a <AppId>
  
     Se a sua conta não possui as [permissões necessárias](#required-permissions) no Active Directory, verá uma mensagem de erro que indica "Authentication_Unauthorized" ou "nenhuma subscrição encontrada no contexto".
    
     Para ambas as opções, é devolvido o novo principal de serviço. O Id do objeto é necessária quando conceder permissões. É necessário o guid listado com os **Nomes principais de serviço** ao iniciar sessão. Este guid é o mesmo valor que o id da aplicação. As aplicações de exemplo, este valor é referido como o **ID de cliente**. 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Conceda as permissões do principal de serviço na sua subscrição. Neste exemplo, adicione o capital de serviço para a função de **leitor** , o que concede permissão de leitura todos os recursos na subscrição. Para outras funções, consulte o artigo [RBAC: funções incorporadas](./active-directory/role-based-access-built-in-roles.md). Para o parâmetro **ServicePrincipalName** , forneça o **ID de objecto** que utilizou quando criar a aplicação. 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Se a sua conta não possui permissões suficientes para atribuir uma função, verá uma mensagem de erro. A mensagem indicar a sua conta **não tem autorização para executar a ação Microsoft.Authorization/roleAssignments/write ao longo do âmbito ' / subscrições / {guid}'**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fornecer certificado através de script do Azure clip automatizado

Agora, tem de iniciar sessão como a aplicação para efetuar operações.

1. Sempre que iniciar sessão como um principal de serviço, tem de fornecer o id do inquilino do diretório da sua aplicação de AD. Um inquilino é uma instância do Active Directory. Para obter o id do inquilino para a sua subscrição atualmente autenticado, utilize:

        azure account show

     Qual devolve:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se o que precisa para obter o id de inquilino do outra subscrição, utilize o seguinte comando:

        azure account show -s {subscription-id}

1. Para obter a impressão digital do certificado e remover desnecessárias carateres, utilize:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Que devolve um valor de impressão digital semelhante a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Se for necessário para obter o id do cliente para utilizar para o início de sessão, utilize:

        azure ad sp show -c exampleapp

     O valor a utilizar para o início de sessão é o guid listado nos nomes principais de serviço.

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. Inicie sessão como o capital de serviço.

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

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
- Para obter mais informações sobre como utilizar certificados e Azure clip, consulte o artigo [autenticação baseada em certificado com Azure Service principais da linha de comandos Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 
