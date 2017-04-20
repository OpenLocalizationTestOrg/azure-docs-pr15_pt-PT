## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Preparar para autenticar pedidos de Gestor de recursos Azure

Tem de autenticar todas as operações que efectuam nos recursos utilizando o [Gestor de recursos Azure] [ lnk-authenticate-arm] com Azure Active Directory (AD). A forma mais fácil de configurar esta está a utilizar o PowerShell ou Azure CLI.

Deverá instalar [Azure PowerShell 1.0] [ lnk-powershell-install] ou posterior antes de continuar.

Os seguintes passos mostram como configurar a autenticação de palavra-passe para uma aplicação de AD a utilização do PowerShell. Pode executar estes comandos numa sessão de PowerShell padrão.

1. Iniciar sessão sua subscrição Azure utilizando o seguinte comando:

    ```
    Login-AzureRmAccount
    ```

2. Tome nota do **TenantId** e **SubscriptionId**. Irá necessitar destes mais tarde.

3. Crie uma nova aplicação de Azure Active Directory utilizando o seguinte comando, substituindo os titulares de local:

    - **{Nome}:** um nome a apresentar para a sua aplicação, tal como **MySampleApp**
    - **{URL da Home page}:** o URL da home page da sua aplicação, tal como **http://mysampleapp/home**. Este URL não é necessário apontar para uma aplicação real.
    - **{Identificador de aplicação}:** Um identificador exclusivo, tal como **http://mysampleapp**. Este URL não é necessário apontar para uma aplicação real.
    - **{Palavra-passe}:** Uma palavra-passe que irá utilizar para autenticar com a sua aplicação.

    ```
    New-AzureRmADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password {Password}
    ```
    
4. Anote o **ApplicationId** da aplicação que criou. Irá necessitar isto mais tarde.

5. Crie um novo serviço principal utilizando o seguinte comando, substituindo **{MyApplicationId}** o **ApplicationId** no passo anterior:

    ```
    New-AzureRmADServicePrincipal -ApplicationId {MyApplicationId}
    ```
    
6. Uma atribuição de funções utilizando o seguinte comando, substituindo **{MyApplicationId}** o **ApplicationId**do programa de configuração.

    ```
    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```
    
Concluiu agora a criação da aplicação de Azure AD permite-lhe autenticar do c# aplicação personalizada. Terá os seguintes valores mais tarde neste tutorial:

- TenantId
- SubscriptionId
- ApplicationId
- Palavra-passe

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: ../articles/powershell-install-configure.md
