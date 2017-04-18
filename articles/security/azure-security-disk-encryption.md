<properties
   pageTitle="Encriptação de Azure do disco para Windows e Linux IaaS VMs | Microsoft Azure"
   description="O papel fornece uma descrição geral do Microsoft Azure disco encriptação para Windows e Linux IaaS VMs."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="krkhan"/>


#<a name="azure-disk-encryption-for-windows-and-linux-iaas-vms"></a>Encriptação de Azure do disco para Windows e Linux IaaS VMs

É vivamente empenhada em assegurar a privacidade de dados, a soberania de dados e a permite controlar o Azure alojado dados através de um intervalo de tecnologias avançadas de para encriptar, controlar e gerir as teclas de encriptação, Microsoft Azure acesso auditoria & de controlo de dados. Fornece clientes Azure flexibilidade para escolher a solução mais adequado às suas necessidades empresariais. Neste documento técnico, vamos irá dar-lhe para uma nova solução de tecnologia "Azure encriptação do disco para Windows e de Linux IaaS VM" para o ajudar a proteger e proteger os seus dados para satisfazer as segurança organizacional e compromissos de conformidade. O papel fornece informações detalhadas sobre como utilizar as funcionalidades de encriptação disco Azure, incluindo os cenários suportados e o utilizador experiências.

**Nota**: algumas recomendações contidas podem resultar em dados maiores, rede ou utilização de recursos de cluster que resulta em custos de licença ou subscrição adicionais.

## <a name="overview"></a>Descrição geral

Encriptação do disco Azure é uma nova funcionalidade que permite-lhe encriptar discos máquina virtual do Windows e Linux IaaS. Encriptação do disco Azure tira partido a funcionalidade de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão do setor do Windows e a funcionalidade de [Encriptação DM](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação de volume do sistema operativo e os discos de dados. A solução está integrada com cofre [Azure chave](https://azure.microsoft.com/documentation/services/key-vault/) para o ajudar a controlar e gerir as chaves de encriptação do disco e segredos na sua subscrição do cofre chave, garantindo que todos os dados no discos máquina virtual são encriptados em repouso no seu armazenamento Azure.

Encriptação de Azure do disco para Windows e Linux IaaS VMs está agora na **Disponibilidade geral** em todas as Azure públicos regiões para VMs padrão e VMs com armazenamento premium.

### <a name="encryption-scenarios"></a>Cenários de encriptação

A solução de encriptação de disco do Azure suporta os seguinte cenários de cliente:

- Activar a encriptação no novo IaaS VMs criada a partir de VHD previamente encriptada e chaves de encriptação
- Activar a encriptação no novo IaaS VMs criado a partir das imagens da galeria do Azure
- Activar a encriptação no existente IaaS VMs em execução no Azure
- Desativar a encriptação no Windows IaaS VMs
- Desativar a encriptação em unidades de dados para Linux IaaS VMs

A solução suporta o seguinte procedimento para IaaS VMs quando ativado no Microsoft Azure:

- Integração com o Azure cofre chave
- Padrão camada VMs - [A, D, DS, G, etc s série IaaS VMs](https://azure.microsoft.com/pricing/details/virtual-machines/)
- Activar a encriptação no Windows e Linux IaaS VMs
- Desativar a encriptação em unidades de sistema operativo e os dados para Windows IaaS VMs
- Desativar a encriptação em unidades de dados para Linux IaaS VMs
- Activar a encriptação no VMs IaaS executar o sistema operativo de cliente do Windows
- Activar a encriptação em volumes com caminhos de montagem
- Activar a encriptação no VMs Linux configurados com sistema RAID baseado no Software 
- Activar a encriptação no Windows VMs configurados com espaços de armazenamento
- São suportadas todas as regiões públicas Azure

A solução não suporta cenários, funcionalidades e tecnologia na versão:

- Camada básica IaaS VMs
- Desativar a encriptação na unidade de sistema operativo para Linux IaaS VMs
- VMs IaaS criado utilizando o método de criação de VM clássico
- Integração com o seu serviço de gestão de chave no local
- Windows Server 2016 pré-visualização técnica não é suportada neste lançamento
- Azure ficheiros (partilha de ficheiro Azure), o sistema de ficheiros de rede (NFS), volumes dinâmicos, Windows VMs configurado com os sistemas RAID baseado no Software


### <a name="encryption-features"></a>Funcionalidades de encriptação

Quando ativar e implementar a encriptação de disco Azure para Azure IaaS VMs, as seguintes capacidades estão ativadas, consoante a configuração fornecida:

- Encriptação do SO volume para proteger o volume de arranque no resto no armazenamento de cliente
- Encriptação de dados do volume/s para proteger os volumes de dados em repouso no armazenamento de cliente
- Desativar a encriptação em unidades de sistema operativo e os dados para Windows IaaS VMs
- Desativar a encriptação em unidades de dados para Linux IaaS VMs
- Salvaguardar os chaves de encriptação e segredos no cliente subscrição cofre chave Azure
- Comunicar o estado da encriptação da VM IaaS encriptada
- Remoção disco configuração de definições de encriptação de máquina IaaS virtual

A encriptação de disco Azure para IaaS VMS para Windows e Linux solução inclui a extensão de encriptação do disco para o Windows, a extensão de encriptação do disco para Linux, os cmdlets do PowerShell de encriptação de disco, os cmdlets do clip de encriptação de disco e modelos de Gestor de recursos do Azure de encriptação do disco. A solução de encriptação do disco Azure é suportada no VMs IaaS a executar o Windows ou Linux SO. Para obter mais detalhes sobre os sistemas operativos suportados, consulte pré-requisitos secção abaixo.

**Nota**: não existe nenhuma gratuitamente para encriptar discos VM com encriptação do Azure do disco.

### <a name="value-proposition"></a>Proposta de valor

A solução de gestão de encriptação do Azure disco Ativa as seguintes as necessidades da empresa na nuvem:

-   IaaS VM é protegido em repouso com a tecnologia de encriptação padrão indústria segurança organizacional endereço e requisitos de conformidade.
-   Arranque de IaaS VM em cliente controlado teclas e políticas e pode auditar a sua utilização nas chave cofre.


### <a name="encryption-workflow"></a>Fluxo de trabalho de encriptação

Os passos de alto níveis necessários para activar a encriptação do disco para Windows e de Linux VM são:

1. Cliente seleciona um cenário de encriptação a partir dos cenários de encriptação acima
2. Especifica a configuração de encriptação e opte para activar a encriptação do disco através do modelo de Gestor de recursos do disco Azure encriptação ou PS cmdlets ou comando clip de cliente

    - Cenário VHD cliente encriptado, os carregamentos de cliente o VHD encriptado ao seu armazenamento conta e encriptação chave material para a sua chave do Cofre de palavras e fornecem a configuração de encriptação para activar a encriptação numa VM IaaS novo
    - Para a nova VM criado a partir da Galeria Azure e VM existente já em execução no Azure, o cliente fornecer a configuração de encriptação para activar a encriptação na VM IaaS

3. Cliente concede acesso a plataforma Azure para ler o material de chave de encriptação (sistemas de teclas de encriptação BitLocker para Windows e frase de acesso para Linux) do seu Cofre chave para ativar a encriptação a VM IaaS
4. Cliente fornecer identidade da aplicação Azure AD para escrever o material de chave de encriptação para os respetivos cofre chave para ativar a encriptação a VM IaaS para cenários mencionados na #2 acima
5.  Azure atualiza o modelo de serviço VM com encriptação e configuração do cofre chave e disposições encriptado VM para o cliente

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig1.png)

### <a name="decryption-workflow"></a>Desencriptação fluxo de trabalho

Os passos de alto níveis necessários para desativar a encriptação do disco para de IaaS VM são:

1. Cliente escolhe desativar a encriptação (desencriptação) numa VM IaaS em execução no Azure através do modelo de Gestor de recursos do disco Azure encriptação ou PS cmdlets e especifica a configuração de desencriptação.
2. O passo de encriptação desativar desativa a encriptação do volume do SO ou dados ou ambos na IaaS VM a execução do Windows. No entanto a encriptação para Linux do disco SO desactivar não é suportada tal como mencionado na documentação acima. O passo desativar só é permitido para unidades de dados no Linux VMs. 
4. Azure atualiza o modelo de serviço VM e a VM IaaS está marcado como desencriptada. O conteúdo da VM não é encriptado em repouso deixem.
5. A operação de encriptação desativar não elimina o Cofre de chave do cliente e o material de chave de encriptação,-BitLocker encriptação teclas para Windows ou frase de acesso para Linux.

## <a name="prerequisites"></a>Pré-requisitos

Seguem-se pré-requisitos para ativar o Azure disco encriptação no Azure IaaS VMs para cenários suportados realçados na secção Descrição geral

- Utilizador tem de ter uma subscrição do Azure active válida para criar os recursos no Azure nas regiões suportados
- Encriptação do disco Azure é suportada no seguinte Windows server do SKU - Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Windows Server 2016 pré-visualização técnica não é suportada neste lançamento.
- Encriptação do disco Azure é suportada no seguinte Windows cliente do SKU - cliente do Windows 8 e Windows 10 cliente.

**Nota**: tem de estar instalado para o Windows Server 2008 R2, .net framework 4,5 antes de ativar encriptação no Azure. Que pode instalá-la a partir do Windows update ao instalar a atualização opcional "Microsoft .NET Framework 4.5.2 para o Windows Server 2008 R2 x64 sistemas ([KB2901983](https://support.microsoft.com/kb/2901983))"

- Encriptação do disco Azure é suportada no seguinte Linux servidor SKUs - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e vermelho chapéu Enterprise Linux.

**Nota**: encriptação de disco Linux SO atualmente é suportada nas seguintes Linux distribuições - RHEL 7.2, CentOS 7.2, Ubuntu 16.04

- Todos os recursos (Ex: cofre chave, armazenamento de conta, VM, etc.,) tem de pertencer à mesma região Azure e subscrição.

**Nota**: encriptação do disco Azure requer o Cofre chave e os VMs residem na mesma região Azure. Configurá-las na região separada causará falha na ativação da funcionalidade de encriptação do disco Azure.

- Configurar e configurar o Azure chave cofre para utilização de encriptação do disco Azure, consulte a secção **configuração e configurar o Azure chave cofre para utilização de encriptação do disco Azure** na *Pré-requisitos da* secção deste artigo.
- Configurar e configurar a aplicação do Azure AD no Azure Active directory para utilização de encriptação do disco Azure, consulte a secção **Configurar a aplicação de AD Azure no Azure Active Directory** na secção de *Pré-requisitos* deste artigo.
- Para instalar e configurar a política de chave cofre acesso para a aplicação de AD Azure, consulte o artigo **política de definição de chave do cofre acesso para a aplicação de AD Azure** na secção *Pré-requisitos* secção deste artigo.
- Para preparar um VHD previamente encriptada do Windows, consulte a secção **preparar um VHD previamente encriptada do Windows** no anexo deste artigo.
- Para preparar um VHD Linux previamente encriptada, consulte a secção **preparar um VHD Linux previamente encriptados** no anexo deste artigo.
- Azure plataforma precisa de aceder à chaves de encriptação ou segredos no cliente Azure chave cofre para disponibilizá-los à máquina virtual arrancar e desencriptar o volume de máquina virtual SO. Para conceder permissões para plataforma do Azure para aceder ao cliente chave cofre, a propriedade de **enabledForDiskEncryption** tem de ser definida na cofre chave para este requisito. Consulte a secção **configuração e configurar o Azure chave cofre para utilização de encriptação do disco Azure** no anexo deste artigo para obter mais detalhes.
- O segredo cofre chave e URLs de chave (KEK) de encriptação de chave tem de ser versões. Azure impõe esta restrição do controlo de versões. Consulte abaixo exemplos para secreta válida e KEK URL:
    - Exemplo de URL secreto válido:   *https://contosovault.vault.azure.net/secrets/BitLockerEncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - Exemplo de válido KRK KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Encriptação do disco Azure não suporta números de porta a ser especificados como parte da chave cofre palavra-passe e KEK URLs. Consulte abaixo exemplos para URL de cofre chave suportados:
    - Deles não aceites chave cofre URL   *https://contosovault.vault.azure.net:443/segredos/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
    - URL do cofre chave aceite:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
- Para ativar o Azure disco encriptação funcionalidade, a VMs IaaS tem de cumprir os seguintes requisitos de configuração de ponto final de rede: 
    - A VM IaaS tem de conseguir ligar ao ponto final do Azure Active Directory \[Login.windows.net\] para obter um token para ligar ao Azure cofre chave
    - A VM IaaS tem de conseguir ligar ao ponto final do Azure chave cofre para escrever as teclas de encriptação cofre chave do cliente
    - A VM IaaS tem de conseguir ligar ao ponto final de armazenamento Azure que aloja a repositório extensão Azure e a sua conta de armazenamento Azure que aloja os ficheiros VHD

**Nota:** Se a política de segurança limita o acesso a partir do Azure VMs Internet, pode resolver o URI acima para o qual precisa de conectividade e configurar uma regra específica para permitir a conectividade de saída para o IPs.

- Utilize a versão mais recente da versão do Azure PowerShell SDK para configurar a encriptação do Azure disco. Transferir a versão mais recente do [Azure PowerShell solte](https://github.com/Azure/azure-powershell/releases)

**Nota:** Azure encriptação de disco não é suportada no [Azure PowerShell SDK versão 1.1.0](https://github.com/Azure/azure-powershell/releases/tag/v1.1.0-January2016). Se está a receber um erro relacionado com através do Azure PowerShell 1.1.0, consulte o artigo [Azure disco encriptação erro relacionado com PowerShell do Azure 1.1.0](http://blogs.msdn.com/b/azuresecurity/archive/2016/02/10/azure-disk-encryption-error-related-to-azure-powershell-1-1-0.aspx).

- Para executar qualquer um dos comandos clip do Azure e associá-la com a sua subscrição Azure, terá primeiro de instalar a versão de Azure clip:
    - Para instalar o clip do Azure e associá-la com a sua subscrição Azure, consulte o artigo [como instalar e configurar o clip do Azure](../xplat-cli-install.md)
    - Utilizar o clip do Azure para Mac, Linux e Windows com o Gestor de recursos do Azure, consulte o artigo [aqui](azure-cli-arm-commands.md)
- Solução de encriptação de disco Azure utilizar BitLocker protector de chave externo para o Windows IaaS VMs. Se o VMs domínio aderido, não emissão quaisquer políticas de grupo impõem componentes de proteção de TPM. Consulte [Este artigo](https://technet.microsoft.com/library/ee706521) para obter detalhes sobre a política de grupo para "Permitir BitLocker sem um TPM compatível".
- O disco Azure encriptação pré-requisito script do PowerShell para criar a aplicação do Azure AD, criar novo cofre chave ou cofre chave existente de configuração e activar a encriptação se encontra [aqui](https://github.com/Azure/azure-powershell/blob/dev/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).

#### <a name="setup-the-azure-ad-application-in-azure-active-directory"></a>Configurar a aplicação do Azure AD no Azure Active Directory

Quando encriptação tem de estar ativada numa VM em execução no Azure, encriptação do disco Azure gera e escreve as chaves de encriptação Cofre de chave. Gerir as chaves de encriptação na chave cofre necessita de autenticação do Azure AD.

Para o efeito, uma aplicação do Azure AD deverá ser criada. Encontrar passos detalhados para registar uma aplicação podem aqui, na secção secção "Obter uma identidade para a aplicação" Esta [mensagem no blogue](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx).  Esta mensagem também contém um número de exemplos úteis sobre aprovisionamento e configurar o seu Cofre chave. Para fins de autenticação, autenticação baseada em secreta de cliente ou autenticação baseada em certificados Azure AD do cliente pode ser utilizada.

##### <a name="client-secret-based-authentication-for-azure-ad"></a>Secreta cliente com base autenticação para Azure AD

As secções que se seguem tem os passos necessários para configurar secreta autenticação baseada num cliente do Azure AD.

##### <a name="create-a-new-azure-ad-app-using-azure-powershell"></a>Criar uma nova aplicação Azure AD através do PowerShell do Azure

Utilizar o cmdlet do PowerShell abaixo para criar uma nova aplicação do Azure AD:

    $aadClientSecret = “yourSecret”
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -Password $aadClientSecret
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

**Nota:** $azureAdApplication.ApplicationId é o AD ClientID Azure e $aadClientSecret é o segredo que deve utilizar mais tarde para ativar ADE de cliente. Deve salvaguardar o segredo de cliente do Azure AD corretamente.


##### <a name="provisioning-the-azure-ad-client-id-and-secret-from-the-azure-classic-deployment-model-portal"></a>O ID de cliente do Azure AD e o segredo a partir do modelo de implementação do Azure clássico Portal de aprovisionamento

Azure AD cliente ID e palavra-passe também podem ser aprovisionados utilizando o modelo de implementação do Azure clássica Portal na https://manage.windowsazure.com, siga os passos abaixo para desempenhar esta tarefa:

1. clique no separador do Active Directory, como apresentado na figura abaixo:

![Encriptação Azure do disco](./media/azure-security-disk-encryption/disk-encryption-fig3.png)

2. clique em Adicionar aplicação e escreva o nome da aplicação, conforme apresentado abaixo:

![Encriptação Azure do disco](./media/azure-security-disk-encryption/disk-encryption-fig4.png)

3. clique no botão de seta e configurar as propriedades da aplicação conforme apresentado abaixo:

![Encriptação Azure do disco](./media/azure-security-disk-encryption/disk-encryption-fig5.png)

4. Clique na marca de verificação no canto inferior esquerdo para concluir. É apresentada a página de configuração da aplicação. Repare que o ID do Azure AD cliente está localizado na parte inferior da página, como apresentado na figura abaixo.

![Encriptação Azure do disco](./media/azure-security-disk-encryption/disk-encryption-fig6.png)

5. Guarde o segredo de cliente do Azure AD ao clicar no botão guardar. Clique no botão Guardar botão e tenha em atenção o segredo da caixa de texto de teclas, este é o segredo de cliente do Azure AD. Deve salvaguardar o segredo de cliente do Azure AD corretamente.

![Encriptação Azure do disco](./media/azure-security-disk-encryption/disk-encryption-fig7.png)


**Nota:** deste fluxo acima não é suportado no Portal.

##### <a name="use-an-existing-app"></a>Utilizar uma aplicação existente

Para executar os comandos abaixo precisa do módulo Azure AD PowerShell, que pode ser obtido a partir de [aqui](https://technet.microsoft.com/library/jj151815.aspx).

**Nota:** os comandos abaixo tem de ser executados a partir de uma nova janela do PowerShell. Não utilize Azure PowerShell ou a janela de Gestor de recursos do Azure executar estes comandos. O motivo para esta recomendação é uma vez que estes cmdlets são no módulo MSOnline ou Azure AD PowerShell.

    $clientSecret = ‘<yourAadClientSecret>’
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type password -Value $clientSecret

#### <a name="certificate-based-authentication-for-azure-ad"></a>Autenticação de certificados com base para Azure AD

> [AZURE.NOTE] Autenticação de certificado baseado AAD não é atualmente suportada no Linux VMs.

As secções que se seguem tem os passos necessários para configurar uma autenticação de certificado baseado para Azure AD.

##### <a name="create-a-new-azure-ad-app"></a>Criar uma nova aplicação do Azure AD

Executar os cmdlets do PowerShell abaixo para criar uma nova aplicação do Azure AD:

**Nota:** Substituir `yourpassword` abaixo de cadeia com a sua palavra-passe segura e salvaguardar a palavra-passe.

    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate("C:\certificates\examplecert.pfx", "yourpassword")
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $azureAdApplication = New-AzureRmADApplication -DisplayName "<Your Application Display Name>" -HomePage "<https://YourApplicationHomePage>" -IdentifierUris "<https://YouApplicationUri>" -KeyValue $keyValue -KeyType AsymmetricX509Cert
    $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId

Depois de concluir este passo, carregar um ficheiro. pfx para cofre chave e ativar a política de acesso necessária para implementar esse certificado para uma VM.

##### <a name="use-an-existing-azure-ad-app"></a>Utilize uma aplicação do Azure AD existente
Se estiver a configurar autenticação de certificados com base para uma aplicação existente, utilize os cmdlets do PowerShell abaixo. Certifique-se executá-los a partir de uma nova janela do PowerShell.

    $certLocalPath = 'C:\certs\myaadapp.cer'
    $aadClientID = '<Client ID of your AAD app>'
    connect-msolservice
    $cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate
    $cer.Import($certLocalPath)
    $binCert = $cer.GetRawCertData()
    $credValue = [System.Convert]::ToBase64String($binCert);
    New-MsolServicePrincipalCredential -AppPrincipalId $aadClientID -Type asymmetric -Value $credValue -Usage verify

Depois de concluir este passo, carregar um ficheiro. pfx para cofre chave e ativar a política de acesso necessária para implementar esse certificado para uma VM.

##### <a name="upload-a-pfx-file-to-key-vault"></a>Carregar um ficheiro PFX para cofre chave
Pode obter esta [mensagem no blogue](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx) para explicação de detalhes sobre como funciona a este processo. No entanto, os cmdlets do PowerShell abaixo são tudo o que precisa para esta tarefa. Certifique-se executá-los a partir da consola do Azure PowerShell:

**Nota:** Substituir `yourpassword` abaixo de cadeia com a sua palavra-passe segura e salvaguardar a palavra-passe.

    $certLocalPath = 'C:\certs\myaadapp.pfx'
    $certPassword = "yourpassword"
    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’

    $fileContentBytes = get-content $certLocalPath -Encoding Byte
    $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

    $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certPassword"
    }
    "@

    $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
    $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

    Switch-AzureMode -Name AzureResourceManager
    $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ResourceGroupName $resourceGroupName –EnabledForDeployment

##### <a name="deploy-a-certificate-in-key-vault-to-an-existing-vm"></a>Implementar um certificado na chave cofre para uma VM existente
Depois de terminar a PFX a carregar, utilize os passos abaixo para implementar um certificado na chave cofre uma VM existente:

    $resourceGroupName = ‘yourResourceGroup’
    $keyVaultName = ‘yourKeyVaultName’
    $keyVaultSecretName = ‘yourAadCertSecretName’
    $vmName = ‘yourVMName’
    $certUrl = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName).Id
    $sourceVaultId = (Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName).ResourceId
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore "My" -CertificateUrl $certUrl
    Update-AzureRmVM -VM $vm  -ResourceGroupName $resourceGroupName


#### <a name="setting-key-vault-access-policy-for-the-azure-ad-application"></a>Definir a política de chave cofre acesso para a aplicação de AD Azure

As aplicações do Azure AD tem direitos para aceder ao teclas ou segredos no cofre. Utilize o cmdlet [Set-AzureKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/dn903607.aspx) para conceder permissões a aplicação, utilizando o Id do cliente (que foi geradas quando a aplicação foi registada) como o valor do parâmetro – ServicePrincipalName. Pode obter [Esta mensagem no blogue](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) , para obter alguns exemplos sobre esse. Abaixo também tem um exemplo de como desempenhar esta tarefa através do PowerShell:

    $keyVaultName = '<yourKeyVaultName>'
    $aadClientID = '<yourAadAppClientID>'
    $rgname = '<yourResourceGroup>'
    Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

**Nota**: encriptação do disco Azure requer que configurar as seguintes políticas de acesso à sua aplicação de cliente AAD - permissões 'WrapKey' e 'Set'

## <a name="terminology"></a>Terminologia

Utilize a tabela de terminologia como referência para compreender alguns dos termos comuns utilizados por esta tecnologia:

| Terminologia           | Definição                                                                                                                                                                                                                                   |
|-----------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure AD                   | Azure AD é o [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/). Conta do Azure AD é uma pré-necessários para autenticar, armazenar e obter segredos do cofre chave.                                                                                                        |
| Azure cofre chave [AKV] | Azure chave Cofre é um serviço de gestão de chaves de criptografia com base em validado FIPS módulos de segurança de Hardware salvaguardar os seus chaves de criptografia e segredos sensíveis a maiúsculas e segura., consulte a documentação de [Chave do cofre](https://azure.microsoft.com/services/key-vault/) para obter mais detalhes.          |
| PROCESSADOR                   | Gestor de recursos do Azure                                                                                                                                                                                                                       |
| BitLocker             | [BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é que uma indústria reconhecido tecnologia de encriptação do volume do Windows utilizada para ativar a encriptação do disco no Windows IaaS VMs                                                                                                                  |
| BEK                   | Chaves de encriptação BitLocker são utilizadas para encriptar o volume de arranque do sistema operativo e volumes de dados. As teclas de BitLocker são salvaguarda num cofre de chave Azure do cliente como segredos.                                                                              |
| CLIP                   | [Interface de comandos Azure](../xplat-cli-install.md)                                                                                                                                                                                                                 |
| Encriptação DM              | [Encriptação DM](https://en.wikipedia.org/wiki/Dm-crypt) é o subsystem de encriptação do disco transparente baseado em Linux utilizado para ativar a encriptação do disco na Linux IaaS VMs                                                                                                                           |
| KEK                   | Chave de encriptação é a chave assimétrica (RSA 2048) utilizada para proteger ou moldar o segredo se pretender. Pode fornecer um protegido por HSM-chave ou protegido por software. Para obter mais detalhes, consulte a documentação do [Azure chave cofre](https://azure.microsoft.com/services/key-vault/) para obter mais detalhes |
| PS cmdlets            | [Cmdlets do PowerShell Azure](powershell-install-configure.md)                                                                                                                                                                                                                                           |

### <a name="setting-and-configuring-azure-key-vault-for-azure-disk-encryption-usage"></a>Configuração e configurar o Azure chave cofre para Azure a utilização de encriptação do disco

Encriptação do disco Azure protege as chaves de encriptação do disco e segredos no Cofre de chave Azure. Siga os passos em cada uma das secções abaixo para a configuração cofre chave para utilização de encriptação do disco Azure.

#### <a name="create-a-new-key-vault"></a>Criar um novo cofre chave
Para criar um novo cofre de tecla, utilize uma das opções listadas em:

- Utilizar o modelo de Gestor de recursos "KeyVault de criar de 101" localizado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/101-create-key-vault/azuredeploy.json)
- Utilize o Azure PowerShell [chave cofre cmdlets](https://msdn.microsoft.com/library/dn868052.aspx).
- Utilize o portal do Gestor de recursos Azure.

**Nota:** Se já tem uma configuração de chave cofre para a sua subscrição, continue para secção seguinte.

![Azure cofre chave](./media/azure-security-disk-encryption/keyvault-portal-fig1.png)

#### <a name="provisioning-a-key-encryption-key-optional"></a>Aprovisionamento uma chave de encriptação chave (opcional)

Se pretender utilizar uma chave de encriptação de chave (KEK) para uma camada adicional de segurança para moldar as chaves de encriptação BitLocker, deve adicionar um KEK para cofre chave para utilizar no processo de aprovisionamento.  Utilize o cmdlet [Adicionar AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868048.aspx) para criar uma nova chave de encriptação de chave na chave cofre. Também pode importar KEK a partir do seu gestão das chaves HSM no local. Para obter mais detalhes, consulte a [documentação de chave do cofre](https://azure.microsoft.com/documentation/services/key-vault/).

    Add-AzureKeyVaultKey [-VaultName] <string> [-Name] <string> -Destination <string> {HSM | Software}

O KEK pode ser adicionado a partir do portal de Gestor de recursos do Azure também utilizar Azure chave cofre UX

![Azure cofre chave](./media/azure-security-disk-encryption/keyvault-portal-fig2.png)

#### <a name="set-key-vault-permissions-to-allow-the-azure-platform-access-to-the-keys-and-secrets"></a>Definir permissões de chave cofre para permitir o acesso de plataforma Azure para as chaves e segredos

A plataforma Azure precisa de aceder à chaves de encriptação ou segredos no Cofre de chave Azure para disponibilizá-los para a VM arrancar e desencriptar os volumes. Para conceder permissões para a plataforma do Azure para que pode aceder a cofre chave, a propriedade de *enabledForDiskEncryption* tem de estar definida no cofre chave. Pode definir a propriedade enabledForDiskEncryption no seu Cofre chave utilizando o Cofre chave PS cmdlet:

    Set-AzureRmKeyVaultAccessPolicy -VaultName <yourVaultName> -ResourceGroupName <yourResourceGroup> -EnabledForDiskEncryption

Também pode definir a propriedade *enabledForDiskEncryption* visitando https://resources.azure.com. Tem de definir a propriedade *enabledForDiskEncryption* no seu Cofre chave tal como mencionado antes. Caso contrário, a implementação irá falhar.

Pode configurar as políticas de acesso para a aplicação de AAD a partir do UX cofre chave:

![Azure cofre chave](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure cofre chave](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

Certifique-se de que a chave cofre estiver ativado para encriptação do disco na "Avançadas as políticas de acesso":

![Azure cofre chave](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)

## <a name="disk-encryption-deployment-scenarios-and-user-experiences"></a>Cenários de implementação de encriptação do disco e experiências de utilizador

Existem muitos cenários que pode ativar a encriptação do disco e os passos podem variar de acordo com o cenário. As secções que se seguem vai abranger mais detalhes estes cenários.

### <a name="enable-encryption-on-new-iaas-vms-created-from-the-azure-gallery"></a>Activar a encriptação no novo VM de IaaS criado a partir da galeria do Azure

Encriptação do disco pode ser ativada no novo VM de Windows IaaS da Galeria Azure no Azure utilizando o modelo de Gestor de recursos publicados [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image). Clique no botão "Implementar para Azure" no modelo de guia de introdução Azure, configuração de encriptação de entrada no pá parâmetros e clique em OK. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, termos legais e contrato e clique em botão Criar para activar a encriptação numa VM IaaS novo.

**Nota:** Este modelo cria uma nova VM Windows encriptada utilizando a imagem da Galeria de Windows Server 2012.

Encriptação do disco pode ser ativada numa nova 7.2 VM do IaaS sistema RedHat Linux com uma matriz de RAID-0 200 GB utilizando [Este](https://aka.ms/fde-rhel) modelo de Gestor de recursos. Depois do modelo é implementado, verifique se a encriptação do VM Estado utilizando o `Get-AzureRmVmDiskEncryptionStatus` cmdlet conforme descrito na secção "[unidade de encriptação SO uma VM Linux em execução](#encrypting-os-drive-on-a-running-linux-vm)". Quando a máquina devolve estado `VMRestartPending`, reinicie a VM.

Pode ver os detalhes de parâmetros de modelo de Gestor de recursos para VM novo a partir do cenário de galeria Azure com o ID de cliente do Azure AD na tabela abaixo:

| Parâmetro                        | Descrição|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| adminUserName                 | Nome de utilizador do administrador para a máquina virtual                                                                                                                           |
| adminPassword                 | Palavra-passe de utilizador de administração para a máquina virtual                                                                                                                       |
| newStorageAccountName         | Nome da conta de armazenamento para armazenar SO e dados VHDs                                                                                                             |
| vmSize                        | Tamanho da VM. Atualmente, é suportado apenas de série de um padrão, D e G                                                                                          |
| virtualNetworkName            | Nome da VNet ao qual o NIC VM deve pertence.                                                                                                            |
| subnetName                    | Nome da sub-rede no vNet à qual deve pertence VM NIC                                                                                               |
| AADClientID                   | ID de cliente da aplicação do Azure AD que tem permissões para escrever segredos cofre chave                                                                                       |
| AADClientSecret               | Secreta de cliente da aplicação do Azure AD que tem permissões para escrever segredos cofre chave                                                                                   |
| keyVaultURL                   | URL do cofre chave para o qual BitLocker chave deve ser carregado para. Pode acedê-lo utilizando o cmdlet: (Get AzureRmKeyVault - VaultName-ResourceGroupName). VaultURI |
| keyEncryptionKeyURL           | URL da chave de codificação que é utilizado para encriptar a chave gerada do BitLocker. Isto é opcional.                                                               |
| keyVaultResourceGroup         | Grupo de recursos do cofre chave                                                               |
| vmName                        | Nome da VM no qual encriptação operação está a ser executado


**Nota:** KeyEncryptionKeyURL é um parâmetro opcional. Pode trazer com os seu próprios KEK a salvaguardar informações suplementares a chave de encriptação de dados (frase secreta) na chave cofre.

### <a name="enable-encryption-on-new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Activar a encriptação no novo VM de IaaS criada a partir de teclas VHD encriptados do cliente e encriptação

Neste cenário pode ativar a encriptação utilizando o Gestor de recursos, os cmdlets do PowerShell ou o modelo comandos clip. As secções abaixo irão explicar mais detalhes, o modelo de Gestor de recursos e comandos do clip.

Siga as instruções de uma das seguintes secções para preparar previamente encriptadas imagens que podem ser utilizadas no Azure. Quando a imagem estiver criada, os passos na secção seguinte podem ser utilizados para criar um VM Azure encriptada.

- [Preparar um VHD previamente encriptada do Windows](#preparing-a-pre-encrypted-windows-vhd)
- [Preparar um VHD Linux previamente encriptada](#preparing-a-pre-encrypted-linux-vhd)

#### <a name="using-resource-manager-template"></a>Utilizar o modelo de Gestor de recursos

Encriptação do disco pode ser ativada no cliente encriptados VHD utilizando o modelo de Gestor de recursos publicados [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm). Clique no botão "Implementar para Azure" no modelo de guia de introdução Azure, configuração de encriptação de entrada no pá parâmetros e clique em OK. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, termos legais e contrato e clique em botão Criar para activar a encriptação no novo IaaS VM.

Os detalhes de parâmetros de modelo de Gestor de recursos do cliente encriptado VHD cenário são descritos na tabela abaixo:

| Parâmetro                        | Descrição|
|-------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| newStorageAccountName | Nome da conta de armazenamento para armazenar encriptado vhd SO. Esta conta de armazenamento deve já foram criada na mesma localização, como a VM e mesmo grupo de recursos                                                     |
| osVhdUri              | URI de SO vhd da conta de armazenamento                                                                                                                                                                                      |
| osType                | Tipo de produto do sistema operativo (Windows/Linux)                                                                                                                                                                                         |
| virtualNetworkName    | Nome da VNet ao qual o NIC VM deve pertence. Isto deveria ter sido já criado na mesma localização, como a VM e mesmo grupo de recursos                                                                     |
| subnetName            | Nome da sub-rede no vNet à qual deve pertence VM NIC                                                                                                                                                     |
| vmSize                | Tamanho da VM. Atualmente, é suportado apenas de série de um padrão, D e G                                                                                                                                                |
| keyVaultResourceID    | ResourceID identificar o recurso cofre chave no braço. Pode acedê-lo utilizando o cmdlet do PowerShell: (Get AzureRmKeyVault - VaultName &lt;yourKeyVaultName&gt; - ResourceGroupName &lt;yourResourceGroupName&gt;). ResourceId |
| keyVaultSecretUrl     | URL da chave de encriptação disco aprovisionada no cofre chave                                                                                                                                                                |
| keyVaultKekUrl        | URL da chave de encriptação de chave que está para encriptar a chave de encriptação gerado disco                                                                                                                                       |
| vmName               | Nome da IaaS VM   |


#### <a name="using-powershell-cmdlets"></a>Utilizar os cmdlets do PowerShell

Encriptação do disco pode ser ativada no cliente encriptados VHD utilizando os cmdlets PS publicados [aqui](https://msdn.microsoft.com/library/azure/mt603746.aspx).  

#### <a name="using-cli-commands"></a>Utilizar comandos de clip

Siga os passos abaixo para ativar a encriptação do disco para este cenário utilizando os comandos do clip:

1. Configurar políticas de acesso no Cofre de chave:
    - Defina o sinalizador de 'EnabledForDiskEncryption':`azure keyvault set-policy --vault-name <keyVaultName> --enabled-for-disk-encryption true`
    - Definir permissões à aplicação de Azure AD para escrever segredos KeyVault:`azure keyvault set-policy --vault-name <keyVaultName> --spn <aadClientID> --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]`
2. Para ativar a encriptação uma VM existentes/em execução, escreva:  *azure vm ativar-encriptação do disco-- grupo de recursos <resourceGroupName> – nome <vmName> id de cliente – aad <aadClientId> segredo de cliente de – aad <aadClientSecret> – disco-encriptação-chave-cofre-url <keyVaultURL> – disco-encriptação-chave-cofre-id <keyVaultResourceId> *
3. Obter o estado de encriptação: *"azure vm Mostrar-disco-encriptação-status--grupo de recursos <resourceGroupName> – nome <vmName> – json"*
4. Para ativar a encriptação uma VM nova a partir do cliente encriptados VHD, utilize a abaixo parâmetros com o comando "azure vm criar":
    - disco-encriptação-chave-cofre-id < disco-encriptação-chave-cofre-id >
    - disco-encriptação-chave-url < disco-encriptação-chave-url >
    - chave de encriptação id--chave-cofre-< chave de encriptação id--chave-cofre->
    - chave de encriptação do chave-url < chave-encriptação-chave-url >


### <a name="enable-encryption-on-existing-or-running-iaas-windows-vm-in-azure"></a>Activar a encriptação no existente ou em execução IaaS Windows VM no Azure

Neste cenário pode ativar a encriptação utilizando o Gestor de recursos, os cmdlets do PowerShell ou o modelo comandos clip. As secções abaixo irão explicam mais detalhes, como ativá-lo a utilizar o modelo de Gestor de recursos e comandos do clip.

#### <a name="using-resource-manager-template"></a>Utilizar o modelo de Gestor de recursos

Encriptação do disco pode ser ativada no existentes/execução IaaS Windows VM no Azure utilizando o modelo de Gestor de recursos publicados [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm). Clique no botão "Implementar para Azure" no modelo de guia de introdução Azure, configuração de encriptação de entrada no pá parâmetros e clique em OK. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, termos legais e contrato e clique em botão Criar para activar a encriptação no existentes/execução IaaS VM.

Os detalhes de parâmetros de modelo de Gestor de recursos do cenário VM existentes/a trabalhar rapidamente com o ID de cliente do Azure AD estão disponíveis na tabela abaixo:

| Parâmetro                 | Descrição|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | ID de cliente da aplicação do Azure AD que tem permissões para escrever segredos cofre chave                                                                                                                                              |
| AADClientSecret         | Secreta de cliente da aplicação do Azure AD que tem permissões para escrever segredos cofre chave                                                                                                                                          |
| keyVaultName | Nome do cofre chave para o qual BitLocker chave deve ser carregado para. Pode acedê-lo utilizando o cmdlet: (Get AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | URL da chave de codificação que é utilizado para encriptar a chave gerada do BitLocker. Isto é opcional se selecionar `nokek` na lista pendente UseExistingKek. Se selecionar `kek` na lista pendente UseExistingKek, tem de introduzir informação o valor de keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Tipo de volume no qual encriptação é efetuada a operação. Os valores válidos são "Sistema operativo", "Dados", "Todos"                                                                                                                     |
| sequenceVersion         | Versão de sequência da operação de BitLocker. Incrementar este número de versão sempre que uma operação de encriptação do disco é executada na mesma VM                                                                             |
| vmName                 | Nome da VM no qual encriptação operação está a ser executado


**Nota:** KeyEncryptionKeyURL é um parâmetro opcional. Pode trazer com os seu próprios KEK a salvaguardar informações suplementares a chave de encriptação de dados (secreta de encriptação BitLocker) na chave cofre.

#### <a name="using-powershell-cmdlets"></a>Utilizar os cmdlets do PowerShell

Consulte o **Azure explorar encriptação de disco com PowerShell do Azure** blogue mensagem [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) para obter detalhes sobre como ativar encriptação através da encriptação de disco Azure utilizar os cmdlets do PS.

#### <a name="using-cli-commands"></a>Utilizar comandos de clip

Siga os passos abaixo para ativar o encriptação existentes/em execução IaaS Windows VM no Azure utilizando os comandos do clip:

1. Configurar políticas de acesso no Cofre de chave:
    - Definir o sinalizador de 'EnabledForDiskEncryption': "azure keyvault definir uma política – cofre nome <keyVaultName> – ativado-para-encriptação do disco true"
    - Definir permissões de aplicação do Azure AD para escrever segredos KeyVault: "azure keyvault definir uma política – cofre nome <keyVaultName> – spn <aadClientID> – teclas de perms [\"todos os\"] – perms para segredos [\"todos os\"]"
2. Para ativar a encriptação uma VM existentes/em execução, escreva:  *azure vm ativar-encriptação do disco-- grupo de recursos <resourceGroupName> – nome <vmName> id de cliente – aad <aadClientId> segredo de cliente de – aad <aadClientSecret> – disco-encriptação-chave-cofre-url <keyVaultURL> – disco-encriptação-chave-cofre-id <keyVaultResourceId> *
3. Obter o estado de encriptação: *"azure vm Mostrar-disco-encriptação-status--grupo de recursos <resourceGroupName> – nome <vmName> – json"*
4. Para ativar a encriptação uma VM nova a partir do cliente encriptados VHD, utilize a abaixo parâmetros com o comando "azure vm criar":
    - disco-encriptação-chave-cofre-id < disco-encriptação-chave-cofre-id >
    - disco-encriptação-chave-url < disco-encriptação-chave-url >
    - chave de encriptação id--chave-cofre-< chave de encriptação id--chave-cofre->
    - chave de encriptação do chave-url < chave-encriptação-chave-url >


### <a name="enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure"></a>Activar a encriptação no existente ou em execução IaaS Linux VM no Azure

Encriptação do disco pode ser ativada no existentes/execução IaaS Linux VM no Azure utilizando o modelo de Gestor de recursos publicados [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). Clique no botão "Implementar para Azure" no modelo de guia de introdução Azure, configuração de encriptação de entrada no pá parâmetros e clique em OK. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, termos legais e contrato e clique em botão Criar para activar a encriptação no existentes/execução IaaS VM.

Os detalhes de parâmetros de modelo de Gestor de recursos do cenário VM existentes/a trabalhar rapidamente com o ID de cliente do Azure AD são descritos na tabela abaixo:

| Parâmetro                 | Descrição|
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AADClientID            | ID de cliente da aplicação do Azure AD que tem permissões para escrever segredos cofre chave                                                                                                                                              |
| AADClientSecret         | Secreta de cliente da aplicação do Azure AD que tem permissões para escrever segredos cofre chave                                                                                                                                          |
| keyVaultName | Nome do cofre chave para o qual BitLocker chave deve ser carregado para. Pode acedê-lo utilizando o cmdlet: (Get AzureRmKeyVault - ResourceGroupName <yourResourceGroupName>). Vaultname   |
|  keyEncryptionKeyURL   | URL da chave de codificação que é utilizado para encriptar a chave gerada do BitLocker. Isto é opcional se selecionar "nokek" na lista pendente UseExistingKek. Se selecionar "kek" na lista pendente UseExistingKek, tem de introduzir informação o valor de keyEncryptionKeyURL                                                                                                                        |
| volumeType             | Tipo de volume no qual encriptação é efetuada a operação. Os valores suportados válidos são "SO" / "Tudo" (para RHEL 7.2, CentOS 7.2 e Ubuntu 16.04) e "Dados" para todos os outros distros.                                                                                                                      |
| sequenceVersion         | Versão de sequência da operação de BitLocker. Incrementar este número de versão sempre que uma operação de encriptação do disco é executada na mesma VM                                                                             |
| vmName                 | Nome da VM no qual encriptação operação está a ser executado
| frase de acesso              | Escreva uma frase de acesso forte como a chave de encriptação de dados                                                                                                                                                                       |                                                                                                                                                                                                                                                      

**Nota:** KeyEncryptionKeyURL é um parâmetro opcional. Pode trazer com os seu próprios KEK a salvaguardar informações suplementares a chave de encriptação de dados (frase secreta) na chave cofre.

#### <a name="cli-commands"></a>Comandos de clip

Encriptação do disco pode ser ativada no cliente encriptados VHD utilizando o comando clip instalado a partir de [aqui](../xplat-cli-install.md). Siga os passos abaixo para ativar o encriptação existentes/em execução IaaS Linux VM no Azure utilizando os comandos do clip:

1. Configurar políticas de acesso no Cofre de chave:
    - Definir o sinalizador de 'EnabledForDiskEncryption': "azure keyvault definir uma política – cofre nome <keyVaultName> – ativado-para-encriptação do disco true"
    - Definir permissões de aplicação do Azure AD para escrever segredos KeyVault: "azure keyvault definir uma política – cofre nome <keyVaultName> – spn <aadClientID> – teclas de perms [\"todos os\"] – perms para segredos [\"todos os\"]"
2. Para ativar a encriptação uma VM existentes/em execução, escreva:  *azure vm ativar-encriptação do disco-- grupo de recursos <resourceGroupName> – nome <vmName> id de cliente – aad <aadClientId> segredo de cliente de – aad <aadClientSecret> – disco-encriptação-chave-cofre-url <keyVaultURL> – disco-encriptação-chave-cofre-id <keyVaultResourceId> *
3. Obter o estado de encriptação: "azure vm Mostrar-disco-encriptação-status--grupo de recursos <resourceGroupName> – nome <vmName> – json"
4. Para ativar a encriptação uma VM nova a partir do cliente encriptados VHD, utilize a abaixo parâmetros com o comando "azure vm criar".
    - *disco-encriptação-chave-cofre-id < disco-encriptação-chave-cofre-id >*
    - *disco-encriptação-chave-url < disco-encriptação-chave-url >*
    - *chave de encriptação id--chave-cofre-< chave de encriptação id--chave-cofre->*
    - *chave de encriptação do chave-url < chave-encriptação-chave-url >*

### <a name="get-encryption-status-of-an-encrypted-iaas-vm"></a>Obter o estado de encriptação de um VM IaaS encriptada

Pode obter o estado de encriptação com o portal de Gestor de recursos do Azure, [os cmdlets do PowerShell](https://msdn.microsoft.com/library/azure/mt622700.aspx) ou comandos clip. As secções abaixo irão explicam como utilizar o portal do Azure e comandos do clip para obter o estado de encriptação.

#### <a name="get-encryption-status-of-an-encrypted-windows-vm-using-azure-resource-manager-portal"></a>Obter o estado de encriptação de um VM encriptada do Windows através do portal do Gestor de recursos do Azure

Pode obter o estado de encriptação da IaaS VM a partir do Gestor de recursos do Azure portal. Iniciar sessão no portal Azure em https://portal.azure.com/, clique na ligação de máquinas virtuais no menu à esquerda para ver a vista de resumo das máquinas virtuais na sua subscrição. Pode filtrar a vista de máquinas virtuais selecionando o nome da subscrição do menu pendente de subscrição. Clique em colunas localizadas na parte superior do menu de página máquinas virtuais. Selecione a coluna de encriptação do disco a partir do pá de coluna de escolha e clique em Atualizar. Deverá ver a coluna de encriptação de disco que mostra o estado de encriptação "Ativado" ou "Não ativado" para cada VM conforme apresentado na figura seguinte.

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig2.png)

#### <a name="get-encryption-status-of-an-encrypted-windowslinux-iaas-vm-using-disk-encryption-ps-cmdlet"></a>Obter o estado de encriptação da VM de IaaS do (Windows/Linux) encriptada utilizar encriptação do disco PS Cmdlet
Pode obter o estado de encriptação da IaaS VM a partir do disco encriptação PS cmdlet "Get-AzureRmVMDiskEncryptionStatus". Para obter as definições de encriptação para sua VM, escreva na sessão Azure PowerShell:

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : NotEncrypted
    DataVolumesEncrypted       : Encrypted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a

O resultado de obter AzureRmVMDiskEncryptionStatus pode que seja inspecionado para encriptação URLs de chave.
    
    C:\> $status = Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMNam
    e $VMName -ExtensionName $ExtensionName
    C:\> $status.OsVolumeEncryptionSettings

    DiskEncryptionKey                                                 KeyEncryptionKey                                               Enabled
    -----------------                                                 ----------------                                               -------
    Microsoft.Azure.Management.Compute.Models.KeyVaultSecretReference Microsoft.Azure.Management.Compute.Models.KeyVaultKeyReference    True


    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey.SecretUrl
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a
    C:\> $status.OsVolumeEncryptionSettings.DiskEncryptionKey
    
    SecretUrl                                                                                                               SourceVault
    ---------                                                                                                               -----------
    https://rheltest1keyvault.vault.azure.net/secrets/bdb6bfb1-5431-4c28-af46-b18d0025ef2a/abebacb83d864a5fa729508315020f8a Microsoft.Azure.Management....

O valor de definições OSVolumeEncrypted e DataVolumesEncrypted estão definidas para "Encriptado" a mostrar que ambos os volumes são encriptados utilizar encriptação Azure do disco. Consulte o **Azure explorar encriptação de disco com PowerShell do Azure** blogue mensagem [parte 1](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) e [parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) para obter detalhes sobre como ativar encriptação através da encriptação de disco Azure utilizar os cmdlets do PS.

**Nota**: no Linux VMs, o `Get-AzureRmVMDiskEncryptionStatus` cmdlet demora minutos de 3-4 para comunicar o estado de encriptação.

#### <a name="get-encryption-status-of-the-iaas-vm-from-disk-encryption-cli-command"></a>Obter o estado de encriptação da IaaS VM de encriptação do disco comando clip

Pode obter o estado de encriptação da IaaS VM de encriptação do disco clip comando *azure vm Mostrar--encriptação de estado do disco*. Para obter as definições de encriptação para sua VM, escreva na sessão Azure clip:

    azure vm show-disk-encryption-status --resource-group <yourResourceGroupName> --name <yourVMName> --json  

#### <a name="disable-encryption-on-running-windows-iaas-vm"></a>Desativar a encriptação sobre como executar o Windows IaaS VM

Pode desativar a encriptação num Windows ou Linux IaaS VM em execução através do modelo de Gestor de recursos do disco Azure encriptação ou PS cmdlets e especifica a configuração de desencriptação.


##### <a name="windows-vm"></a>Windows VM

O passo de encriptação desativar desativa a encriptação do volume do SO ou dados ou ambos na IaaS VM a execução do Windows. Não é possível desativar o volume de SO e deixe o volume de dados encriptado. Quando o passo de encriptação desativar é executado, modelo de implementação clássica Azure atualiza o modelo de serviço VM e a VM IaaS do Windows está marcado como desencriptada. O conteúdo da VM não é encriptado em repouso deixem. A encriptação desativar não elimina o Cofre de chave do cliente e o material de chave de encriptação, que é BitLocker encriptação teclas para Windows e frase de acesso para Linux. 

##### <a name="linux-vm"></a>Linux VM

O passo de encriptação desativar desativa a encriptação do volume dados na VM de IaaS Linux em execução

**Nota**: desactivar a encriptação no disco do sistema operativo não é permitido no Linux VMs.

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-resource-manager-template"></a>Desativar a encriptação existentes/execução IaaS VM no Azure utilizando o modelo de Gestor de recursos

Encriptação do disco pode ser desativada sobre como executar o Windows IaaS VM utilizando o modelo de Gestor de recursos publicados [aqui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm). Clique no botão "Implementar para Azure" no modelo de guia de introdução Azure, configuração do teclado desencriptação no pá parâmetros e clique em OK. Selecione a subscrição, grupo de recursos, localização do grupo de recursos, termos legais e contrato e clique em botão Criar para activar a encriptação numa VM IaaS novo.

Para Linux VM, [Este](https://aka.ms/decrypt-linuxvm) modelo pode ser utilizado para desativar a encriptação.

Detalhes de parâmetros de modelo de Gestor de recursos para desactivar a encriptação sobre como executar IaaS VM:

| vmName         | Nome da VM no qual encriptação operação está a ser executado                                                                                                                                                                       |
|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| volumeType     | Tipo de volume no qual desencriptação é efetuada a operação. Os valores válidos são "Sistema operativo", "Dados", "Todos". **Nota:** Não é possível desativar a encriptação sobre como executar o volume de Windows IaaS VM SO/arranque sem desativar encriptação volume "Dados". **Nota**: desactivar a encriptação no disco do sistema operativo não é permitido no Linux VMs. |
| sequenceVersion | Versão de sequência da operação de BitLocker. Incrementar este número de versão sempre que uma operação de desencriptação disco é executada na mesma VM                                                                                          |

##### <a name="disable-encryption-on-existingrunning-iaas-vm-in-azure-using-ps-cmdlet"></a>Desativar a encriptação existentes/execução IaaS VM no Azure utilizando PS cmdlet

Para desativar utilizando o cmdlet PS, [Desativar AzureRmVMDiskEncryption](https://msdn.microsoft.com/library/azure/mt715776.aspx) cmdlet desativa encriptação uma infraestrutura como uma máquina de virtual do serviço (IaaS). Este cmdlet suporta Windows e Linux VMs. Este cmdlet instala uma extensão na máquina virtual para desativar a encriptação. Se o parâmetro nome não for especificado, é criada uma extensão com o nome predefinido "AzureDiskEncryption para Windows VMs". 

No Linux VMs, é utilizada a extensão "AzureDiskEncryptionForLinux".

**Nota**: Este cmdlet reinicia máquina virtual. 

## <a name="appendix"></a>Anexo

### <a name="connect-to-your-subscription"></a>Ligar à sua subscrição

Certifique-se rever a secção de *Pré-requisitos* neste documento antes de continuar. Depois de se certificar de que todos os pré-requisitos foram preenchidos, siga os passos abaixo para se ligar à sua subscrição:

1. iniciar uma sessão de PowerShell do Azure e inicie sessão na sua conta Azure com o seguinte comando:

    Login-AzureRmAccount

2. se tem múltiplas subscrições e pretende especificar um específico a utilizar, escreva o seguinte para ver as subscrições para a sua conta:

    Get-AzureRmSubscription

3. para especificar a subscrição que pretende utilizar, escreva:

    Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>

4. para verificar a subscrição configurada está correta, escreva:

    Get-AzureRmSubscription

5. para confirmar que os cmdlets do Azure disco encriptação estão instalados, escreva:

    Get-command *diskencryption*

6 deverá ver os abaixo saída confirmar a instalação do Azure disco encriptação PowerShell:

    PS C:\Windows\System32\WindowsPowerShell\v1.0> get-command *diskencryption*
    CommandType  Name                                         Source                                                             
    Cmdlet       Get-AzureRmVMDiskEncryptionStatus            AzureRM.Compute                                                    
    Cmdlet       Disable-AzureRmVMDiskEncryption              AzureRM.Compute                                                    
    Cmdlet       Set-AzureRmVMDiskEncryptionExtension         AzureRM.Compute                                                     

### <a name="preparing-a-pre-encrypted-windows-vhd"></a>Preparar um VHD previamente encriptada do Windows
As secções que se seguem são necessárias para se preparar um VHD previamente encriptada do Windows para implementação como um VHD encriptada no Azure IaaS. Os passos são utilizados para preparar e windows fresca VM (vhd) no Hyper-V ou Azure de arranque.

#### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política de grupo para permitir que não sejam TPM para protecção SO
Tem de configurar a definição de política de grupo BitLocker denominada encriptação de unidade BitLocker, localizado em política de computador Local \Computer Configuration\Administrative administrativos\Componentes componentes. Alterar esta definição para: *Unidades do sistema operativo - exigir autenticação adicional no arranque - permitir o BitLocker sem um TPM compatível* conforme apresentado na figura abaixo:

![Microsoft Antimalware no Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

#### <a name="install-bitlocker-feature-components"></a>Instalar componentes de funcionalidade do BitLocker
Para o Windows Server 2012 e acima de utilizar a abaixo comando:

    dism /online /Enable-Feature /all /FeatureName:Bitlocker /quiet /norestart

Para utilização do Windows Server 2008 R2 a abaixo comando:

    ServerManagerCmd -install BitLockers

#### <a name="prepare-os-volume-for-bitlocker-using-bdehdcfg"></a>Preparar a volume SO BitLocker utilização`bdehdcfg`

Execute o comando abaixo para comprimir partição do SO e preparar o computador para o BitLocker.

    bdehdcfg -target c: shrink -quiet

#### <a name="using-bitlocker-to-protect-the-os-volume"></a>Utilização do BitLocker para proteger o volume de SO
Utilizar o [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) comando para activar a encriptação no volume de arranque utilizando um protector de chave externo e coloque a chave externa (ficheiro .bek) na unidade externa ou volume. Encriptação irão estar ativada no volume de arranque do sistema após o reinício seguinte.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

**Nota:** A VM tem de ser preparado com um vhd separada/recurso de dados para obter a chave externa a utilização do BitLocker.

#### <a name="encrypting-os-drive-on-a-running-linux-vm"></a>Encriptar unidade SO numa VM Linux em execução

Encriptação de unidade SO uma VM Linux em execução é suportada no distros seguinte:

- RHEL 7.2
- CentOS 7.2
- Ubuntu 16.04

Pré-requisitos para encriptação de disco do SO:

- Tem de ser criado VM da imagem da Galeria de Azure no portal do Gestor de recursos do Azure.
- Azure VM com, pelo menos, 4 GB de RAM (recomendado tamanho é 7 GB).
- (Para RHEL e CentOS) SELinux tem de ser [desactivado](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) a VM. A VM tem de ser reiniciada pelo menos uma vez após a desativação SELinux.


##### <a name="steps"></a>Passos

1. Crie uma VM utilizando um dos distros especificado acima.

Para CentOS 7.2, encriptação de disco do sistema operativo é suportada através de uma imagem especial. Para utilizar esta imagem, especifique "7.2n", como o Sku quando criar a VM:

    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"

2. configure a VM de acordo com as suas necessidades. Se passar para encriptar todos os dados unidades tem de ser /etc/fstab especificado e montável a partir de unidades de (SO + dados).

> [AZURE.NOTE] Tem de utilizar UUID = … para especificar as unidades de dados no/etc/fstab em vez de especificando o dispositivo nome do bloco, por exemplo, /dev/sdb1. Durante a encriptação irá alterar a ordem das unidades na VM. Se a VM depender de uma ordem específica de dispositivos do bloco de falhará de montagem-los depois da encriptação.

3. sessões de SSH termine a sessão.

4. para encriptar o SO, especifique volumeType como "todos os" ou "SO" quando [Activar a encriptação](#enable-encryption-on-existing-or-running-iaas-linux-vm-in-azure).

> [AZURE.NOTE] Todos os processos de espaço de utilizador que não estão a ser executado como `systemd` serviços devem ser mortos com um `SIGKILL`. A VM deve ser reiniciada. Fórum planeia tempo de inatividade da VM quando SO activação encriptação uma VM a execução do disco.

5. periodicamente monitorize o progresso de encriptação utilizando as instruções na [secção seguinte](#monitoring-os-encryption-progress).

6. depois de obter AzureRmVmDiskEncryptionStatus mostra "VMRestartPending", reinicie o seu VM por uma das funcionalidade de registo para-la ou através do PowerShell/Portal/clip.

    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName
    
    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, please reboot the VM

Recomenda-se para guardar [Diagnósticos de arranque](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/) da VM *antes de* reiniciar o computador.

#### <a name="monitoring-os-encryption-progress"></a>Monitorizar o progresso de encriptação do SO

Existem três formas para monitorizar o progresso de encriptação SO.

1. Utilize o cmdlet Get-AzureRmVmDiskEncryptionStatus e verificar se o campo ProgressMessage: 
 
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started

Assim que a VM atinge "SO disco encriptação iniciada" irá demorar cerca de 40-50 minutos um armazenamento Premium cópias VM.

Devido ao [problema #388](https://github.com/Azure/WALinuxAgent/issues/388) nos WALinuxAgent, `OsVolumeEncrypted` e `DataVolumesEncrypted` apresentadas como `Unknown` no algumas distros. Com WALinuxAgent versão 2.1.5 e acima esta serão corrigida automaticamente. No caso de vir `Unknown` no resultado, pode verificar o estado de encriptação do disco utilizando o Visualizador do Azure recurso.

Ir para o [Visualizador de recursos do Azure](https://resources.azure.com/)e, em seguida, expanda este hierarquia no painel de seleção esquerda:

~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

Na InstanceView, desloque-se para baixo para ver o estado de encriptação dos seus discos.

![Vista de instância VM](./media/azure-security-disk-encryption/vm-instanceview.png)

2. Observe [Diagnósticos de arranque](https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/). Mensagens de extensão ADE devem ter o prefixo `[AzureDiskEncryption]`.

3. início de sessão para a VM via SSH e obter o registo de extensão a partir do

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

Não é recomendado para iniciar sessão para a VM enquanto SO encriptação está em curso. Por isso, os registos devem ser copiados apenas quando outros dois métodos tem ocorrido uma falha.

#### <a name="preparing-a-pre-encrypted-linux-vhd"></a>Preparar um VHD Linux previamente encriptada

##### <a name="ubuntu-16"></a>16 de Ubuntu

###### <a name="configure-encryption-during-distro-install"></a>Configurar a encriptação durante a instalação distro

1. Selecione "Configurar volumes encriptados" quando criar partições de discos.

![Configuração de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Crie uma unidade de arranque separada que não deve estar encriptada. Encripte a unidade de raiz.

![Configuração de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. fornecem uma frase de acesso. Esta é a frase de acesso que o utilizador irá carregar para KeyVault.

![Configuração de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. concluir a partições.

![Configuração de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. quando iniciar a VM, lhe-á pedido para uma frase de acesso. Utilize a frase de acesso fornecido no passo 3.

![Configuração de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6 preparar VM para carregar para o Azure utilizando [estas instruções](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Não executa o último passo (deprovisioning a VM) ainda.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurar a encriptação para funcionar com o Azure

1. Crie um ficheiro em /usr/local/sbin/azure_crypt_key.sh, com o conteúdo no script abaixo. Paga a atenção para KeyFileName, porque é o nome do ficheiro frase colocar por Azure.

    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do
        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi


2. Altere a configuração de encriptação no */etc/crypttab*. Deverá ter com aspeto da seguinte forma:

    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh

3. Se estiver a editar o *azure_crypt_key.sh* no Windows e copiou-as para Linux, não se esqueça de executar *dos2unix /usr/local/sbin/azure_crypt_key.sh*.

4. Adicione executáveis permissões para o script:

    chmod +x /usr/local/sbin/azure_crypt_key.sh

4. edite */etc/initramfs-tools/modules* ao acrescentar linhas:

    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1

5. executar `update-initramfs -u -k all` para atualizar a initramfs para tornar o `keyscript` entre em vigor.
6 agora pode deprovision a VM.

![Configuração de Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

7. continue para o passo seguinte e [carregue o seu VHD](#upload-encrypted-vhd-to-an-azure-storage-account) para Azure.

##### <a name="opensuse-132"></a>openSUSE 13.2

###### <a name="configure-encryption-during-distro-install"></a>Configurar a encriptação durante a instalação distro

1. Selecione "Encriptar Volume grupo" quando criar partições de discos. Fornece uma frase de acesso. Esta é a frase de acesso que o utilizador irá carregar para KeyVault.

![openSUSE 13.2 de configuração](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. a VM utilizando a frase-passe de arranque.

![openSUSE 13.2 de configuração](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Preparar VM para carregar para o Azure utilizando [estas instruções](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Não executa o último passo (deprovisioning a VM) ainda.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurar a encriptação para funcionar com o Azure

1. Edite o /etc/dracut.conf e adicione a linha seguinte:

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2. comentar essas linhas no final do ficheiro "/ usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3. acrescentar a linha seguinte no início do ficheiro "/ usr/lib/dracut/modules.d/90crypt/parse-crypt.sh"

    DRACUT_SYSTEMD=0

e alterar todas as ocorrências

    if [ -z "$DRACUT_SYSTEMD" ]; then

para

    if [ 1 ]; then

4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescentar isto após dispositivo"LUKS abrir de #"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5. executar o "/ sbin/usr/dracut - f - v" para atualizar a initrd.

6 agora pode deprovision a VM e, em seguida, [carregue o seu VHD](#upload-encrypted-vhd-to-an-azure-storage-account) para Azure.

##### <a name="centos-7"></a>CentOS 7

###### <a name="configure-encryption-during-distro-install"></a>Configurar a encriptação durante a instalação distro

1. Selecione "os meus dados encripta" quando criar partições de discos.

![Configuração de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Certifique-se de "Encriptar" está selecionada para partição de raiz.

![Configuração de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. fornecem uma frase de acesso. Esta é a frase de acesso que o utilizador irá carregar para KeyVault.

![Configuração de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. quando iniciar a VM, lhe-á pedido para uma frase de acesso. Utilize a frase de acesso fornecido no passo 3.

![Configuração de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Preparar VM para carregar para o Azure utilizando [estas instruções](https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Não executa o último passo (deprovisioning a VM) ainda.

6 agora pode deprovision a VM e, em seguida, [carregue o seu VHD](#upload-encrypted-vhd-to-an-azure-storage-account) para Azure.

###### <a name="configure-encryption-to-work-with-azure"></a>Configurar a encriptação para funcionar com o Azure

1. Edite o /etc/dracut.conf e adicione a linha seguinte:

    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"

2. comentar essas linhas no final do ficheiro "/ usr/lib/dracut/modules.d/90crypt/module-setup.sh":

    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator



3. acrescentar a linha seguinte no início do ficheiro "/ usr/lib/dracut/modules.d/90crypt/parse-crypt.sh"

    DRACUT_SYSTEMD=0

e alterar todas as ocorrências

    if [ -z "$DRACUT_SYSTEMD" ]; then

para

    if [ 1 ]; then

4. Editar /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh e acrescentar isto após dispositivo"LUKS abrir de #"

    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done


5. executar o "/ sbin/usr/dracut - f - v" para atualizar a initrd.

![Configuração de centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

### <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Carregar VHD encriptada para uma conta de armazenamento Azure
Assim que a encriptação BitLocker encriptação pr DM encriptação está ativada, o local VHD encriptado tem de ser carregado para a sua conta de armazenamento.

    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]

### <a name="upload-disk-encryption-secret-for-the-pre-encrypted-vm-to-key-vault"></a>Carregar o segredo de encriptação do disco para a pré-encriptada VM cofre chave
O segredo de encriptação do disco obtido anteriormente necessita de ser carregado como uma palavra-passe num chave cofre. O Cofre de chave tem de ter permissões ativadas para o cliente AAD assim como a encriptação do disco.


    $AadClientId = "YourAADClientId"
    $AadClientSecret = "YourAADClientSecret"

    $KeyVault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
    Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption


#### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Secreta de encriptação de disco não encriptada com uma KEK
Utilize [Conjunto AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) para aprovisionar o segredo na chave cofre. Em caso de uma máquina de virtual do Windows, o ficheiro de bek é codificado como uma cadeia base64 e, em seguida, carregado para cofre chave utilizando o cmdlet Set-AzureKeyVaultSecret. Para Linux, a frase de acesso é codificado como uma cadeia base64 e, em seguida, carregado para chave cofre. Além disso, certifique-se de que os códigos seguintes estão definidos ao criar o segredo no chave cofre.

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $secretName = [guid]::NewGuid().ToString()
    $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
    $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

    $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
    $secretUrl = $secret.Id

O `$secretUrl` deve ser utilizado no próximo passo para [anexar o disco SO sem utilizar KEK](#without-using-a-kek).

#### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Secreta de encriptação do disco encriptada com uma KEK

O segredo, opcionalmente, pode ser encriptado com uma chave de encriptação chave antes de carregar cofre chave. Utilize a moldagem de texto [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) para encriptar pela primeira vez o segredo utilizando a chave de encriptação. O resultado desta operação de moldagem de texto é uma cadeia de codificação de URL base64 que é carregada, em seguida, como uma palavra-passe utilizando o cmdlet [Set-AzureKeyVaultSecret](https://msdn.microsoft.com/library/dn868050.aspx) .

    # This is the passphrase that was provided for encryption during distro install
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id

O `$KeyEncryptionKey` e `$secretUrl` deve ser utilizado no próximo passo para [anexar o disco de SO utilizando KEK](#using-a-kek).

### <a name="specify-secret-url-when-attaching-os-disk"></a>Especificar o URL secreta quando anexar SO disco

#### <a name="without-using-a-kek"></a>Sem utilizar um KEK

Enquanto o disco de sistema operativo, a anexar `$secretUrl` tem de ser passado. O URL foi gerado na secção ["secreta de encriptação de disco não encriptados com uma KEK"](#disk-encryption-secret-not-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl

#### <a name="using-a-kek"></a>Utilizar um KEK

Enquanto o disco de sistema operativo, a anexar `$KeyEncryptionKey` e `$secretUrl` precisa de ser passado. O URL foi gerado na secção ["secreta de encriptação do disco encriptados com uma KEK"](#disk-encryption-secret-encrypted-with-a-kek).

    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id

## <a name="download-this-guide"></a>Transferir este guia
Pode transferir este guia a partir da [Galeria do TechNet](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).


## <a name="for-more-information"></a>Para obter mais informações
[Explorar o disco Azure encriptação com o Azure PowerShell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/16/explore-azure-disk-encryption-with-azure-powershell.aspx?wa=wsignin1.0)

[Explorar o disco Azure encriptação com o PowerShell Azure - parte 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx)
