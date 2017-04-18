<properties
    pageTitle="Implementar uma VM com um certificado utilizando Azure pilha chave Cofre | Microsoft Azure"
    description="Saiba como implementar uma VM e inserção um certificado do Azure pilha chave Cofre"
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
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>Criar VMs e incluir certificados obtidos a partir do Cofre de tecla

Na pilha de Azure, VMs são implementadas através do Gestor de recursos do Azure e agora pode armazenar certificados no Azure pilha de chave do cofre. Em seguida, Azure pilha (Microsoft.Compute fornecedor de recursos para ser específico) emite-los para o seu VMs quando os VMs são implementados. Certificados podem ser utilizados em muitos cenários, incluindo SSL, encriptação e a autenticação de certificados com base.

Ao utilizar este método, pode manter o certificado de segurança. É agora não na imagem VM ou ficheiros de configuração da aplicação ou noutro local não seguros. Ao definir a política de acesso adequado para o Cofre chave, também pode controlar quem recebe acesso ao seu certificado. Outra vantagem é que pode gerir todos os seus certificados num só lugar no Azure pilha de chave do cofre.

Eis uma rápida descrição geral do processo:

-   Precisa de um certificado no formato. pfx.

-   Crie uma chave cofre (utilizando um modelo ou o script de exemplo seguinte).

-   Certifique-se de que ativaram o parâmetro EnabledForDeployment.

-   Carregue o certificado como uma palavra-passe.

## <a name="deploying-vms"></a>Implementar VMs

Este exemplo de script cria uma chave cofre e, em seguida, armazena um certificado armazenado no ficheiro. pfx num diretório local, ao Cofre de palavras chave como uma palavra-passe.

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

A primeira parte do script lê o ficheiro. pfx e, em seguida, armazena-lo como um JSON objeto com o ficheiro conteúdo codificação base64. Em seguida, o objeto JSON também é codificada base64.

Em seguida, cria um novo grupo de recursos e, em seguida, cria uma chave cofre. Tenha em atenção o último parâmetro para o comando novo AzureKeyVault, '-EnabledForDeployment', que concede acesso Azure (especificamente para o fornecedor de recurso Microsoft.Compute) para ler segredos da chave do Cofre de palavras para implementações.

O último comando armazena simplesmente o objeto JSON codificada base64 no Cofre de palavras chave como uma palavra-passe.

Eis a saída de exemplo a partir do script anterior:

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

Agora estamos prontos para implementar um modelo de VM. Tenha em atenção o URI do segredo a partir de saída (como realçados no resultado anterior verde).

Terá de um modelo localizado aqui. Os parâmetros de interesse especiais (para além dos parâmetros VM habituais) são o nome do cofre, o grupo de recursos do cofre e o URI secreto. Obviamente, também pode transferi-lo a partir do GitHub e modificar conforme necessário.

Quando esta VM é implementado, Azure injete o certificado para a VM.
No Windows, certificados no ficheiro. pfx são adicionados com a chave privada não. O certificado é adicionado à localização de certificado LocalMachine, com o arquivo de certificados que o utilizador fornecido. No Linux, o ficheiro de certificado é colocado em diretório /var/lib/waagent, com o nome do ficheiro &lt;UppercaseThumbprint&gt;. crt para o X509 ficheiro de certificado e &lt;UppercaseThumbprint&gt;.prv para a chave privada.
Ambos os ficheiros são .pem formatado.

Normalmente, a aplicação encontra o certificado ao utilizar a impressão digital e não necessita de modificação.

## <a name="retiring-certificates"></a>Reforma certificados


Na secção anterior, podemos mostrava como um novo certificado de emissão para sua VMs existentes. Mas o certificado antigo ainda se encontra na VM e não pode ser removido. Para segurança adicional, pode alterar o atributo para segredo antigo para 'Desactivado', para que o mesmo se um modelo antigo tenta criar uma VM com esta versão antiga do certificado, irá. Eis como configurar uma versão secreta específica para ser desativados:

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>Conclusão


Com este método novo, o certificado pode ser separado a partir da imagem VM ou a carga de útil de aplicação. Por isso, removemos um ponto de exposição.

O certificado também pode ser renovado e carregado para chave cofre sem ter de criar novamente a imagem VM ou o pacote de implementação de aplicação. A aplicação ainda precisa de ser fornecido com o novo URI para esta nova versão do certificado embora.

Separando o certificado da VM ou a carga de útil de aplicação, vamos agora reduzidas o número de funcionários que terão acesso direto o certificado.

Como benefício adicionado, tem agora um local conveniente nos cofre chave para gerir todos os seus certificados, incluindo as todas as versões que foram implementadas ao longo do tempo.

## <a name="next-steps"></a>Próximos passos

[Implementar uma VM com uma palavra-passe do cofre chave](azure-stack-kv-deploy-vm-with-secret.md)

[Permitir que uma aplicação aceder à chave Cofre](azure-stack-kv-sample-app.md)