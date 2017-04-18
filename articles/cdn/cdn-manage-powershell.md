<properties
    pageTitle="Gerir CDN Azure com PowerShell | Microsoft Azure"
    description="Saiba como utilizar os cmdlets do Azure PowerShell para gerir Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="casoper"/>


# <a name="manage-azure-cdn-with-powershell"></a>Gerir CDN Azure com PowerShell

PowerShell fornece um dos métodos mais flexíveis para gerir perfis de Azure CDN e os pontos finais.  Pode utilizar o PowerShell forma interativa ou ao escrever scripts para automatizar tarefas de gestão.  Neste tutorial demonstra várias as tarefas mais comuns que pode realizar com o PowerShell para gerir os perfis de Azure CDN e os pontos finais.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar o PowerShell para gerir os perfis de Azure CDN e os pontos finais, tem de ter o módulo Azure PowerShell instalado.  Para saber como instalar o Azure PowerShell e ligar a Azure utilizando o `Login-AzureRmAccount` cmdlet, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

>[AZURE.IMPORTANT] Tem de iniciar sessão com `Login-AzureRmAccount` antes de pode executar os cmdlets do Azure PowerShell.

## <a name="listing-the-azure-cdn-cmdlets"></a>Listar os cmdlets do Azure CDN

Pode listar todos os cmdlets do Azure CDN utilizando o `Get-Command` cmdlet.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Obter ajuda

Pode obter ajuda com qualquer um destes cmdlets utilizando o `Get-Help` cmdlet.  `Get-Help`Fornece a utilização e sintaxe e mostra, opcionalmente, exemplos.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Lista perfis de Azure CDN existentes

O `Get-AzureRmCdnProfile` cmdlet sem quaisquer parâmetros obtém todos os perfis CDN existentes.

```powershell
Get-AzureRmCdnProfile
```

Este resultado pode ser encaminhado para cmdlets para enumeração.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Também pode devolver um único perfil, especificando o grupo de nome e recursos de perfil.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

>[AZURE.TIP] É possível ter vários perfis CDN com o mesmo nome, desde que estiverem na diferentes grupos de recursos.  Por omitir a `ResourceGroupName` parâmetro devolve todos os perfis com um nome correspondente.

## <a name="listing-existing-cdn-endpoints"></a>Lista existentes CDN os pontos finais

`Get-AzureRmCdnEndpoint`Pode obter um extremo individual ou todos os pontos finais num perfil.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Criação de perfis CDN e os pontos finais

`New-AzureRmCdnProfile`e `New-AzureRmCdnEndpoint` são utilizadas para criar perfis CDN e os pontos finais.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Verificar disponibilidade de nome de ponto final

`Get-AzureRmCdnEndpointNameAvailability`Devolve um objeto que indica se um nome do ponto final está disponível.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Adicionar um domínio personalizado

`New-AzureRmCdnCustomDomain`Adiciona um nome de domínio personalizado para um ponto final existente.

>[AZURE.IMPORTANT] Tem de configurar o CNAME com o seu fornecedor DNS, conforme descrito no [artigo como mapear domínio personalizado para o ponto final de rede de entrega de conteúdos (CDN)](./cdn-map-content-to-custom-domain.md).  Pode testar o mapeamento antes de modificar o ponto final utilizando `Test-AzureRmCdnCustomDomain`.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modificar um ponto final

`Set-AzureRmCdnEndpoint`Modifica um extremo existente.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Limpar/versão-loading CDN activos

`Unpublish-AzureRmCdnEndpointContent`Limpa em cache dos recursos, enquanto `Publish-AzureRmCdnEndpointContent` previamente carrega elementos em pontos finais suportados.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Pontos finais de CDN começando/parar
`Start-AzureRmCdnEndpoint`e `Stop-AzureRmCdnEndpoint` podem ser utilizados para iniciar e parar os pontos finais individuais ou grupos de pontos finais.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Elimina recursos CDN

`Remove-AzureRmCdnProfile`e `Remove-AzureRmCdnEndpoint` podem ser utilizados para remover perfis e os pontos finais.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Próximos passos

Saiba como automatizar Azure CDN com [.NET](./cdn-app-dev-net.md) ou [Node.js](./cdn-app-dev-node.md).

Para obter informações sobre as funcionalidades CDN, consulte o artigo [Descrição geral de CDN](./cdn-overview.md).


