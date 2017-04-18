<properties
   pageTitle="Pretender estada configuração para descrição geral do Azure | Microsoft Azure"
   description="Descrição geral para utilizar a alça de extensão do Microsoft Azure para a configuração do PowerShell pretendido estado. Incluindo pré-requisitos, arquitetura, cmdlets..."
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

# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao processador de extensão Azure pretendido estado configuração #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Agente de VM Azure e as extensões associadas fazem parte dos serviços de infraestrutura do Microsoft Azure. Extensões VM são componentes de software que aumentam a funcionalidade VM e simplificam a várias operações de gestão de VM. Por exemplo, a extensão de VMAccess pode ser utilizada para repor a palavra-passe de um administrador, ou a extensão de Script personalizado pode ser utilizada para executar um script na VM.

Este artigo apresenta a extensão do PowerShell pretendido estado configuração (DSC) para Azure VMs como parte do Azure PowerShell SDK. Pode utilizar os cmdlets do novo para carregar e aplicar uma configuração PowerShell DSC numa VM Azure ativado com a extensão do PowerShell DSC. As PowerShell DSC extensão chamadas para o PowerShell DSC transponha a configuração de DSC recebida na VM. Esta funcionalidade também está disponível através do portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos ##
**Computador local** Para interagir com a extensão do Azure VM, é necessário utilizar o portal do Azure ou o SDK do PowerShell Azure. 

**Agente de convidado** A VM Azure que vai ser configurados pela configuração DSC tem de estar um sistema operativo que suporte em Windows Management Framework (WMF) 4.0 ou 5.0. A lista completa das versões do SO suportadas pode ser encontrada no [Histórico de versões DSC extensão](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termos e conceitos ##
Este guia pressupõe familiaridade com os conceitos seguintes:

Configuração - um documento de configuração de DSC. 

Nó - um alvo para uma configuração DSC. Neste documento, "nó" sempre que se refere a um VM Azure.

Configuração - um .psd1 de ficheiros de dados que contém os dados ambientais para uma configuração

## <a name="architectural-overview"></a>Descrição geral da arquitectura ##

A extensão do Azure DSC utiliza a arquitetura do Azure VM agente entregar, envolva e elaborar relatórios sobre configurações DSC em execução no Azure VMs. A extensão de DSC espera um ficheiro. zip que contém, pelo menos, um documento de configuração e um conjunto de parâmetros fornecido através do SDK do PowerShell Azure ou através do portal do Azure.

Quando a extensão chama-se pela primeira vez, é executado um processo de instalação. Este processo instala uma versão da Windows Management Framework (WMF) utilizando a seguinte lógica:

1. Se o sistema operativo utilizado do Azure VM for Windows Server 2016, não é assumida nenhuma ação. Windows Server 2016 já tem a versão mais recente do PowerShell instalado.
2. Se o `wmfVersion` propriedade for especificada, que versão da WMF esteja instalada, a menos que é incompatível com SO a VM.
3. Se não `wmfVersion` propriedade está especificada, a versão mais recente aplicável da WMF está instalada.

Instalação do WMF requer um reinício. Após reiniciar o computador, a extensão transfere o ficheiro. zip especificado na `modulesUrl` propriedade. Se esta localização é no armazenamento de Blobs do Azure, um token de SA pode ser especificado na `sasToken` propriedade para aceder ao ficheiro. Depois de. zip é transferido e descompactados, a função de configuração definida no `configurationFunction` é executado para gerar o ficheiro MOF. Em seguida, executa a extensão `Start-DscConfiguration -Force` no ficheiro MOF gerado. A extensão para capturar a saída e escritas-o novamente para o canal de estado do Azure. A partir deste momento, a MMC DSC trata monitorização e correção como habitualmente. 

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell ##

Os cmdlets do PowerShell podem ser utilizado com processador ou ASM para compactar, publicar e monitorizar implementações do DSC extensão. Os seguintes cmdlets enumerados são os módulos ASM, mas pode ser substituído "Azure" com "AzureRm" para utilizar o modelo de processador. Por exemplo, `Publish-AzureVMDscConfiguration` utiliza ASM, onde `Publish-AzureRmVMDscConfiguration` utiliza processador. 

`Publish-AzureVMDscConfiguration`leva-o até num ficheiro de configuração, analisa-lo para recursos de DSC dependentes e cria um ficheiro. zip que contém a configuração e recursos DSC necessários para validar a configuração. Também pode criar o pacote localmente utilizando o `-ConfigurationArchivePath` parâmetro. Caso contrário, publica o ficheiro. zip ao armazenamento de Blobs do Azure e seguro-lo com um token de SA.

O ficheiro. zip criado por este cmdlet tem o script de configuração. ps1 na raiz da pasta de arquivo. Os recursos têm a pasta de módulo colocada na pasta de arquivo. 

`Set-AzureVMDscExtension`injete as definições necessárias pela extensão do PowerShell DSC num objecto do configuração VM, em seguida, pode ser aplicado a uma VM Azure com `Update-AzureVM`.

`Get-AzureVMDscExtension`obtém o estado de extensão DSC de uma determinada VM. 

`Get-AzureVMDscExtensionStatus`obtém o estado da configuração do DSC activada pelo processador de extensão DSC. Esta ação pode ser executada num único VM ou grupo de VMs.

`Remove-AzureVMDscExtension`Remove a alça de extensão a partir de uma determinada máquina de virtual. Este cmdlet **remover a configuração,** desinstalar a WMF ou alterar as definições de aplicado na máquina virtual. Remove apenas a alça de extensão. 

**Principais diferenças no cmdlets ASM e processador**

- Cmdlets de processador são síncronos. Cmdlets ASM são assíncronos.
- ResourceGroupName, VMName, ArchiveStorageAccountName, versão e a localização estão todos os parâmetros necessários novos.
- ArchiveResourceGroupName é um novo parâmetro opcional para processador. Este parâmetro pode especificar quando a sua conta de armazenamento pertence a um grupo de recursos diferente daquele onde a máquina virtual é criada.
- ConfigurationArchive chama-se ArchiveBlobName no braço
- ContainerName chama-se ArchiveContainerName no braço
- StorageEndpointSuffix chama-se ArchiveStorageEndpointSuffix no braço
- O parâmetro de atualização automática foi adicionado à processador para ativar as atualizações automáticas do processador extensão para a versão mais recente como e quando está disponível. Note que este parâmetro tem potencial para fazer com que reinicia na VM quando uma nova versão da WMF for disponibilizada. 


## <a name="azure-portal-functionality"></a>Funcionalidade de portal Azure ##
Navegue para uma VM clássica. Em Definições -> clique em geral "Extensões." É criado um novo painel. Clique em "Adicionar" e selecione PowerShell DSC.

O portal necessita de entrada.
**Configuração módulos ou Script**: Este campo é obrigatório. Requer um ficheiro. ps1 que contenha um script de configuração, ou um ficheiro. zip com um script de configuração. ps1 a raiz e todos os recursos dependentes nas pastas do módulo dentro. zip. Podem ser criada com o `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet incluído no Azure PowerShell SDK. O ficheiro. zip é carregado para o seu armazenamento blob do utilizador protegido por um token de SA. 

**Ficheiro de configuração de dados PSD1**: Este campo é opcional. Se a sua configuração requer um ficheiro de dados de configuração no .psd1, utilize este campo para a selecionar e carregue-o para o armazenamento de Blobs do utilizador, onde está protegida por um token de SA. 
 
**Module-Qualified de configuração do nome**:. ps1 ficheiros podem ter várias funções de configuração. Introduza o nome do script. ps1 configuração seguido de um '\' e o nome da função configuração. Por exemplo, se o script. ps1 tiver configuration.ps1"nome" e a configuração é "IisInstall", irá introduzir:`configuration.ps1\IisInstall`

**Os argumentos de configuração**: se a função de configuração incluir argumentos, introduza-as na aqui no formato `argumentName1=value1,argumentName2=value2`. Nota que este formato é um formato diferente como argumentos de configuração são aceites através de modelos de Gestor de recursos ou os cmdlets do PowerShell. 

## <a name="getting-started"></a>Introdução ##

A extensão do Azure DSC leva em documentos de configuração de DSC e enacts-los no Azure VMs. Segue-se um exemplo simples de uma configuração. Guardá-lo localmente como "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

Coloque o script IisInstall.ps1 na VM especificada os passos seguintes, executar a configuração e o report novamente no estado.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## <a name="logging"></a>Funcionalidade de registo ##

Os registos são colocados na:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[número da versão]

## <a name="next-steps"></a>Próximos passos ##

Para mais informações sobre DSC PowerShell, [visite o Centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examine o [modelo de Gestor de recursos do Azure para a extensão de DSC](virtual-machines-windows-extensions-dsc-template.md
). 

Para encontrar funcionalidades adicionais pode gerir com DSC PowerShell, [procure a galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para obter mais recursos DSC.

Para obter detalhes sobre passando sensíveis a maiúsculas e parâmetros para configurações, consulte o artigo [Gerir credenciais em segurança com a alça de extensão DSC](virtual-machines-windows-extensions-dsc-credentials.md).
