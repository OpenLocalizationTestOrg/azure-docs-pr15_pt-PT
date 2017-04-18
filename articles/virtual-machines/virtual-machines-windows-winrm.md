<properties
    pageTitle="Configurar o acesso de WinRM para máquinas virtuais no Gestor de recursos do Azure | Microsoft Azure"
    description="Como configurar o acesso de WinRM para utilização com uma máquina de virtual do Gestor de recursos do Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="singhkay"/>

# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configurar o acesso de WinRM para máquinas virtuais no Gestor de recursos do Azure

## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM na gestão de serviço do Azure vs Gestor de recursos do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássico

* Para obter uma descrição geral do Gestor de recursos Azure, consulte este [artigo](../azure-resource-manager/resource-group-overview.md)
* Diferenças entre o serviço de gestão de Azure e o Gestor de recursos do Azure, consulte este [artigo](../resource-manager-deployment-model.md)

A diferença chave em configurar o WinRM configuração entre as duas pilhas é como o certificado obtém instalado na VM. Na pilha de Gestor de recursos do Azure, os certificados são considerados recursos geridos pelo fornecedor de recursos de chave do cofre. Por conseguinte, o utilizador tem de fornecer o seu próprio certificado e carregue-o para uma cofre chave antes de o utilizar numa VM.

Eis os passos que precisa de tomar para configurar uma VM com a conectividade de WinRM

1. Criar uma chave Cofre
2. Criar um certificado autoassinado
3. Carregue o seu certificado autoassinado para cofre chave
4. Obter o URL para o seu certificado autoassinado no cofre chave
5. O URL da sua autoassinado certificados de referência enquanto cria uma VM

## <a name="step-1-create-a-key-vault"></a>Passo 1: Criar uma chave Cofre

Pode utilizar a abaixo comando para criar o Cofre chave

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Passo 2: Criar um certificado autoassinado
Pode criar um certificado autoassinado utilizando este script do PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Passo 3: Carregar o certificado autoassinado para cofre chave

Antes de carregar o certificado para o Cofre chave que criou no passo 1, tem de converter para um formato o fornecedor de recurso Microsoft.Compute compreendam. A abaixo PowerShell script permitirá fazê-lo

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Passo 4: Obter o URL para o seu certificado autoassinado no cofre chave

O fornecedor de recurso Microsoft.Compute necessita de um URL para o segredo dentro cofre chave enquanto a VM de aprovisionamento. Permite que o fornecedor de recurso Microsoft.Compute transferir o segredo e criar um certificado do equivalente na VM.

>[AZURE.NOTE]O URL do segredo tem de incluir também a versão. Um URL de exemplo aspeto abaixo https://contosovault.vault.azure.net:443/segredos/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### <a name="templates"></a>Modelos

Pode obter a ligação para o URL no modelo, usando os abaixo código

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell

Pode obter este URL utilizando a abaixo comando PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Passo 5: Referenciar o seu URL de certificados autoassinado enquanto cria uma VM

#### <a name="azure-resource-manager-templates"></a>Modelos de Gestor de recursos do Azure

Ao criar uma VM através de modelos, o certificado obtém referenciado na secção segredos e a secção de winRM como abaixo:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Um modelo de exemplo para o acima pode ser encontrado aqui na [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Código de origem para este modelo pode ser encontrado na [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell

    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Passo 6: Ligar-se para a VM
Antes de poder ligar para a VM terá Certifique-se o seu computador esteja configurado para a gestão remota WinRM. Iniciar o PowerShell como administrador e executar a abaixo comando para se certificar de está a configurar.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Poderá ter para se certificar de que o serviço WinRM está em execução se anteriores não funciona. Que pode fazer essa com`Get-Service WinRM`

Depois de concluir o processo de configuração, pode ligar-se à VM utilizando a abaixo comando

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate