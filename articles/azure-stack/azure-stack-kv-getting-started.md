<properties
    pageTitle="Introdução ao Azure pilha chave Cofre | Microsoft Azure"
    description="Começar a utilizar o Azure pilha chave Cofre"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>Introdução ao Cofre chave

Esta secção descreve os passos para criar um cofre, gerir teclas e segredos, bem como autorizar utilizadores ou aplicações ao invocar operações no cofre na pilha de Azure. Os seguintes passos partem do pressuposto de existe uma subscrição de inquilino e KeyVault serviço está registado dentro desse subscrição. Todos os comandos de exemplo sejam baseiam os cmdlets KeyVault disponíveis como parte do Azure PowerShell SDK.

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>Ativar a subscrição de inquilino para operações de cofre 

Antes de as pode emitir operações contra qualquer cofre, é necessário para se certificar de que a sua subscrição estiver ativada para operações de cofre. Pode confirmar que através da emissão o seguinte comando PowerShell:

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

O resultado do comando acima deve relatar "Registado" para o estado "Registo" da todas as linhas.

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 Se não for o caso, deverá chamar o seguinte comando para registar o serviço de KeyVault dentro da sua subscrição:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

E o seguinte é o resultado do comando:
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] Se obtiver o erro: "*a subscrição não estiver registada com Azure tecla cofre*" quando invocar KeyVault cmdlets, confirme que ativou o fornecedor de recurso KeyVault por instruções acima.


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>Criar um contentor puro (cofre) na pilha de Azure para armazenar e gerir chaves de criptografia e segredos

Para criar um cofre, um inquilino primeiro deve criar um grupo de recursos. Os seguintes comandos do PowerShell criam um grupo de recursos e, em seguida, cofre nesse grupo de recursos. O exemplo também inclui a saída normal a partir desse cmdlet.

### <a name="creating-a-resource-group"></a>Criar um grupo de recursos:

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

Resultado:

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>Criar um cofre:


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

Resultado:

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
O resultado deste cmdlet mostra as propriedades do cofre chave que acabou de criar. As duas propriedades mais importantes são:

-   **Nome do cofre**: no exemplo, esta é **vault010**. Irá utilizar este nome para outras cmdlets chave cofre.

-   **URI cofre**: no exemplo, esta é https://vault010.vault.azurestack.local. As aplicações que utilizam cofre através do seu REST API tem de utilizar este URI.

A conta Azure agora está autorizada a efetuar qualquer operações deste cofre chave. Ainda mais ninguém é.


## <a name="operating-on-keys-and-secrets"></a>A funcionar com chaves e segredos

Depois de criar um cofre, siga a seguir passos para criar gerir teclas e segredos:

### <a name="creating-a-key"></a>Criar uma chave

Para criar uma chave, utilize a **Adicionar AzureKeyVaultKey** , por exemplo abaixo. Após a criação de chave efetuada com êxito, o cmdlet irá saída os detalhes de chave recentemente criados.

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

Segue-se o resultado do cmdlet *AzureKeyVaultKey adicionar* :

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
Agora pode fazer referência a esta chave que criado ou carregado para Azure chave cofre, utilizando o respetivo URI. Utilize **as teclas/https://vault010.vault.azurestack.local:443/keyVaultKeyName001** para obter sempre a versão atual; e utilize **https://vault010.vault.azurestack.local:443/teclas/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff** para obter esta versão específica.

### <a name="retrieving-a-key"></a>Obter uma chave

Utilize a **Obter AzureKeyVaultKey** para obter uma chave e respectivos detalhes pelo exemplo seguinte:

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

Segue-se o resultado de obter AzureKeyVaultKey

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>Definir uma palavra-passe

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
Saída

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>Obter uma palavra-passe

    Get-AzureKeyVaultSecret -VaultName vault010

Saída

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

Agora, está pronta para aplicações para utilizar a chave cofre e chave nem secreta.
Tem de autorizar aplicações utilizá-las.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorize a aplicação para utilizar a chave ou palavra-passe 

Para autorizar a aplicação para aceder à chave ou palavra-passe no cofre, utilize o cmdlet Set -**AzureRmKeyVaultAccessPolicy** .

Por exemplo, se o seu nome de cofre é *ContosoKeyVault* e a aplicação que pretende autorizar tem um *ID de cliente* do *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*e pretende autorizar a aplicação para desencriptar e inicie sessão com as teclas no cofre, execute o seguinte:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se pretender autorizar nesse mesmo pedido ler segredos no cofre, execute o seguinte:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>Próximos passos
[Implementar uma VM com uma palavra-passe do cofre chave](azure-stack-kv-deploy-vm-with-secret.md)

[Implementar uma VM com um certificado de chave Cofre](azure-stack-kv-push-secret-into-vm.md)