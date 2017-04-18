<properties
   pageTitle="Prisma credenciais para Azure utilizando DSC | Microsoft Azure"
   description="Descrição geral sobre passagem em segurança de credenciais para Azure máquinas virtuais utilizando a configuração do estado pretendido PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="09/15/2016"
   ms.author="zachal"/>

# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>Prisma credenciais para a alça de extensão Azure DSC #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artigo aborda a extensão de configuração de estado pretendido para Azure. Uma descrição geral de processador de extensão a DSC pode ser encontrada em [Introdução ao processador de extensão Azure configuração de estado pretendido](virtual-machines-windows-extensions-dsc-overview.md). 


## <a name="passing-in-credentials"></a>Prisma credenciais
Como parte do processo de configuração, pode precisar de configurar contas de utilizador, aceder a serviços, ou instale um programa no contexto de utilizador. Para executar estes pontos, tem de fornecer as credenciais. 

DSC permite para configurações parametrizadas credenciais em que são transmitidas para a configuração de forma segura armazenadas em ficheiros MOF. Processador de extensão a Azure simplifica gestão de credenciais, fornecendo gestão automática dos certificados. 

Considere o seguinte script de configuração de DSC cria uma conta de utilizador local com a palavra-passe especificada:

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

É importante incluir *nó localhost* como parte da configuração. Se esta declaração de estiver em falta, os passos seguintes não funcionam como processador de extensão procura especificamente a declaração de localhost nó. Também é importante incluir typecast *[PsCredential]*, tal como este tipo específico de accionadores a extensão para encriptar as credenciais. 

Publica este script ao armazenamento blob:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Definir a extensão do Azure DSC e fornecer a credencial:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>Como são protegidas os credenciais
A executar este código pede-lhe para credenciais. Assim que este software é fornecido, este é armazenado na memória brevemente. Quando é publicado com `Set-AzureVmDscExtension` cmdlet,-é transmitido por HTTPS para a VM, onde Azure armazena encriptadas no disco, utilizando o certificado VM local. Em seguida, é brevemente desencriptar na memória e encriptar novamente para transmiti-lo para DSC.

Este comportamento é diferente de [configurações seguras sem a alça de extensão a utilizar](https://msdn.microsoft.com/powershell/dsc/securemof). O ambiente do Azure dá uma forma para transmitir dados de configuração de forma segura através de certificados. Ao utilizar a alça de extensão DSC, não é necessário para fornecer $CertificatePath ou um $CertificateID / entrada $Thumbprint ConfigurationData.


## <a name="next-steps"></a>Próximos passos ##

Para mais informações sobre a alça de extensão Azure DSC, consulte [Introdução ao processador de extensão Azure configuração de estado pretendido](virtual-machines-windows-extensions-dsc-overview.md). 

Examine o [modelo de Gestor de recursos do Azure para a extensão de DSC](virtual-machines-windows-extensions-dsc-template.md).

Para mais informações sobre DSC PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Para encontrar funcionalidades adicionais pode gerir com DSC PowerShell, [procure a galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para obter mais recursos DSC.
