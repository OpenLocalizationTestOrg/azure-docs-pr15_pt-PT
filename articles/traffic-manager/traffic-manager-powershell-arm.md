<properties
    pageTitle="Suporte de Gestor de recursos para o Gestor de tráfego Azure | Microsoft Azure "
    description="Utilizar o PowerShell para o Gestor de tráfego com o Gestor de recursos Azure"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Suporte de Gestor de recursos para o Gestor de tráfego Azure Azure

Gestor de recursos do Azure é a interface de gestão preferido nos serviços do Azure. Azure perfis do Gestor de tráfego podem ser geridos utilizando APIs com base no Gestor de recursos do Azure e ferramentas.

## <a name="resource-model"></a>Modelo de recurso

Gestor de tráfego Azure está configurado utilizando um conjunto de definições denominado um perfil do Gestor de tráfego. Este perfil contém definições DNS, definições de encaminhamento de tráfego, definições de monitorização de ponto final e uma lista de pontos finais de serviço para o qual o tráfego é encaminhado.

Cada perfil do Gestor de tráfego é representado por um recurso de tipo 'TrafficManagerProfiles'. Ao nível da REST API, o URI de cada perfil é:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Comparação com a API clássico de Gestor de tráfego do Azure

O suporte técnico do Gestor de recursos do Azure para o Gestor de tráfego utiliza terminologia diferente que o modelo de implementação clássica. A tabela seguinte mostra as diferenças entre os termos de Gestor de recursos e clássico:

| Termos de Gestor de recursos | Termos clássico |
|-----------------------|--------------|
| Método de encaminhamento de tráfego | Método de balanceamento de carga |
| Método de prioridade | Método de activação pós-falha |
| Método de ponderada | Método de round robin |
| Método de desempenho | Método de desempenho |

Com base nos comentários, foi alterado a terminologia para melhorar a clareza e reduzir equívocos comuns. Não existe nenhuma diferença de funcionalidade.

## <a name="limitations"></a>Limitações

Ao referenciar um ponto final do tipo 'AzureEndpoints' para uma aplicação Web, pontos finais de Gestor de tráfego só podem referenciar a predefinição (produção) [ranhura Web App](../app-service-web/web-sites-staged-publishing.md). Faixas personalizadas não são suportadas. Como solução, podem ser configuradas faixas personalizadas com o tipo 'ExternalEndpoints'.

## <a name="setting-up-azure-powershell"></a>Configurar o PowerShell do Azure

Estas instruções utilizam o Microsoft Azure PowerShell. O seguinte artigo explica como instalar e configurar o Azure PowerShell.

- [Como instalar e configurar PowerShell do Azure](../powershell-install-configure.md)

Os exemplos neste artigo presumem que tem um grupo de recursos existente. Pode criar um grupo de recursos utilizando o seguinte comando:

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Gestor de recursos do Azure requer que todos os grupos de recurso tenham uma localização. Esta localização é utilizada como predefinição para recursos criados nesse grupo de recursos. No entanto, uma vez que são recursos de perfil do Gestor de tráfego global, não regionais, a escolha da localização do grupo de recursos não tem impacto no Gestor de tráfego do Azure.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de tráfego

Para criar um perfil do Gestor de tráfego, utilize o cmdlet AzureRmTrafficManagerProfile novo:

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A tabela seguinte descreve os parâmetros:

| Parâmetro | Descrição |
|-----------|-------------|
| Nome | O nome do recurso para o recurso de perfil do Gestor de tráfego. Os perfis do mesmo grupo de recursos tem de ter nomes exclusivos. Este nome está separado do nome DNS utilizado para consultas de DNS.|
| ResourceGroupName | O nome do grupo de recursos que contém o recurso de perfil.|
| TrafficRoutingMethod | Especifica o método de encaminhamento de tráfego utilizado para determinar o ponto final é devolvido em resposta uma consulta de DNS. Valores possíveis são 'Desempenho', 'Ponderado' ou 'Priority'.|
| RelativeDnsName | Especifica a parte do nome do anfitrião do nome DNS fornecido por este perfil do Gestor de tráfego. Este valor é combinado com o nome de domínio DNS utilizado pelo Azure tráfego gestor para formar o nome de domínio completamente qualificado (FQDN) do perfil. Por exemplo, definir o valor de 'contoso' fica 'contoso.trafficmanager.net'.|
| TTL | Especifica o DNS Time-para-Live (TTL), em segundos. Este TTL informa a resoluções de Local DNS e quanto tempo para respostas a cache de DNS para este perfil de Gestor de tráfego de clientes de DNS.|
| MonitorProtocol | Especifica o protocolo para utilizar para monitorizar o estado de funcionamento do ponto final. Os valores possíveis são 'HTTP' e 'HTTPS'.|
| MonitorPort | Especifica a porta TCP utilizada para monitorizar o estado de funcionamento do ponto final.|
| MonitorPath | Especifica o caminho relativamente ao nome de domínio de ponto final utilizado para sonda de estado de funcionamento do ponto final.|

O cmdlet cria um perfil do Gestor de tráfego no Azure e devolve um objeto de perfil correspondente ao PowerShell. Neste momento, o perfil não contiver qualquer os pontos finais. Para mais informações sobre como adicionar pontos finais para um perfil do Gestor de tráfego, consulte o artigo [Adicionar os pontos finais de tráfego Gestor](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Obter um perfil do Gestor de tráfego

Para obter um objeto de perfil do Gestor de tráfego existente, utilize o cmdlet Get-AzureRmTrafficManagerProfle:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Este cmdlet devolve um objeto de perfil do Gestor de tráfego.

## <a name="update-a-traffic-manager-profile"></a>Atualizar um perfil do Gestor de tráfego

Modificar os perfis do Gestor de tráfego segue um processo passo 3:

1. Obter o perfil utilizando Get-AzureRmTrafficManagerProfile ou utilize o perfil devolvido pelo novo AzureRmTrafficManagerProfile.
2. Modificar o perfil. Pode adicionar e remover os pontos finais ou alterar os parâmetros de ponto final ou perfil. Estas alterações são operações offline. Apenas está a alterar o local objeto na memória que representa o perfil.
3. Consolide as suas alterações utilizando o cmdlet Set-AzureRmTrafficManagerProfile.

Todas as propriedades de perfil podem ser alteradas exceto RelativeDnsName o perfil. Para alterar o RelativeDnsName, tem de eliminar o perfil e um novo perfil com um novo nome.

O exemplo seguinte demonstra como alterar TTL o perfil:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Existem três tipos de pontos finais de Gestor de tráfego:

1. **Azure pontos finais** são os serviços de alojado no Azure
2. **Os pontos finais externos** são os serviços de alojado fora do Azure
3. **Os pontos finais de aninhadas** são utilizadas para construir aninhadas hierarquias de perfis do Gestor de tráfego. Os pontos finais aninhados activar o encaminhamento de tráfego configurações avançadas para aplicações complexas.

Em todos os três casos, podem ser adicionados os pontos finais de duas maneiras:

1. Utilizar um processo de 3-passo descrito anteriormente. A vantagem deste método é que podem ser efetuadas várias alterações de ponto final numa única actualização.
2. Utilizar o cmdlet AzureRmTrafficManagerEndpoint novo. Este cmdlet adiciona um ponto final a um perfil existente do Gestor de tráfego numa única operação.

## <a name="adding-azure-endpoints"></a>Adicionar pontos finais Azure

Azure pontos finais de referência serviços hospedados no Azure. Três tipos de Azure pontos finais são suportados:

1. Azure Web Apps
2. 'Clássico' cloud services (que podem conter um serviço de PaaS ou máquinas virtuais de IaaS)
3. Recursos PublicIpAddress Azure (que podem ser anexados um balanceador de carga ou uma máquina de virtual NIC). O PublicIpAddress tem de ter um nome DNS atribuído para ser utilizado no Gestor de tráfego.

Em cada caso:

- O serviço é especificado utilizando o parâmetro 'targetResourceId' de adicionar AzureRmTrafficManagerEndpointConfig ou AzureRmTrafficManagerEndpoint novo.
- A 'Alvo' e 'EndpointLocation' estão implícitas pelo TargetResourceId.
- Especificar o 'Peso' é opcional. Espessura é utilizada apenas se o perfil estiver configurado para utilizar o método de encaminhamento de tráfego de 'Ponderado'. Caso contrário, são ignorados. Se especificado, o valor tem de ser um número entre 1 e 1000. O valor predefinido é '1'.
- Especificar o 'Priority' é opcional. As prioridades são utilizadas apenas se o perfil estiver configurado para utilizar o método de encaminhamento de tráfego de 'Priority'. Caso contrário, são ignorados. Os valores válidos são de 1 a 1000 com valores inferiores a indicar que uma prioridade mais alta. Se especificado para um ponto final, tem de ser especificados para todos os pontos finais. Se for omitido, valores predefinidos a partir do '1' são aplicados pela ordem em que os pontos finais são listados.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exemplo 1: Adicionar pontos finais Web App utilizando Adicionar AzureRmTrafficManagerEndpointConfig

Neste exemplo, vamos criar um perfil do Gestor de tráfego e adicione dois pontos finais de Web App utilizando o cmdlet AzureRmTrafficManagerEndpointConfig adicionar.

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar um ponto final de serviço na nuvem 'clássico' utilizando AzureRmTrafficManagerEndpoint novo

Neste exemplo, um ponto final de serviço em nuvem 'clássico' é adicionado a um perfil de Gestor de tráfego. Neste exemplo, vamos especificados o perfil utilizando os nomes de grupo de recursos de perfil e, em vez de prisma um objeto de perfil. As duas abordagens são suportadas.

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 3: Adicionar um ponto final de publicIpAddress AzureRmTrafficManagerEndpoint novo a utilizar

Neste exemplo, um recurso de endereço IP público é adicionado para o perfil do Gestor de tráfego. O endereço IP público tem de ter um nome de DNS configurado e pode estar vinculado ao NIC de uma VM ou para um balanceador de carga.

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>Adicionar pontos finais externos

Gestor de tráfego utiliza os pontos finais externos para direcionar tráfego para serviços alojado fora do Azure. Tal como acontece com os pontos finais Azure, pontos finais externos podem ser adicionados ou utilizando Adicionar-AzureRmTrafficManagerEndpointConfig seguido conjunto AzureRmTrafficManagerProfile ou AzureRMTrafficManagerEndpoint novo.

Quando especificar os pontos finais externos:

- O nome de domínio do ponto final deve ser especificado usando o parâmetro 'Alvo'
- Se for utilizado o método de encaminhamento de tráfego de 'Desempenho', 'EndpointLocation é necessário. Caso contrário, é opcional. O valor tem de ser um [nome de região Azure válida](https://azure.microsoft.com/regions/).
- O 'Peso' e 'Priority' são opcionais.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Adicionar externos pontos finais utilizando Adicionar AzureRmTrafficManagerEndpointConfig e AzureRmTrafficManagerProfile conjunto

Neste exemplo, vamos criar um perfil do Gestor de tráfego, adicionar dois pontos finais externos e consolidar as alterações.

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar externos pontos finais de utilizar o novo AzureRmTrafficManagerEndpoint

Neste exemplo, vamos adicionar um ponto final externo a um perfil existente. O perfil é especificado utilizando os nomes de grupo de recursos e de perfil.

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>Adicionar pontos finais de 'Aninhadas'

Cada perfil do Gestor de tráfego Especifica um método de encaminhamento de tráfego único. No entanto, existem cenários que requerem que o encaminhamento fornecida por um perfil do Gestor de tráfego única a encaminhamento de tráfego mais sofisticadas. Pode aninhar perfis do Gestor de tráfego para combinar as vantagens de mais do que um método de encaminhamento de tráfego. Perfis aninhados permitem-lhe substituir o comportamento de tráfego gestor predefinido para suporte maior e mais complexas implementações da aplicação. Para obter exemplos mais detalhados, consulte o artigo [perfis do Gestor de tráfego aninhada](traffic-manager-nested-profiles.md).

Os pontos finais aninhados estão configurados o perfil principal, utilizando um tipo de ponto final específico, 'NestedEndpoints'. Quando especificar os pontos finais aninhados:

- O ponto final deve ser especificado usando o parâmetro 'targetResourceId'
- Se for utilizado o método de encaminhamento de tráfego de 'Desempenho', 'EndpointLocation é necessário. Caso contrário, é opcional. O valor tem de ser um [nome de região Azure válida](http://azure.microsoft.com/regions/).
- O 'Peso' e 'Priority' são opcionais, como para os pontos finais Azure.
- O parâmetro 'MinChildEndpoints' é opcional. O valor predefinido é '1'. Se o número de pontos finais disponíveis se situar abaixo este limite, o perfil principal considera o perfil subordinado 'degradado' e encaminha as o tráfego para os pontos finais no perfil de elemento principal.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Adicionar pontos finais aninhados utilizando Adicionar AzureRmTrafficManagerEndpointConfig e AzureRmTrafficManagerProfile conjunto

Neste exemplo, vamos criar novo subordinado do Gestor de tráfego e principal perfis, adicionar a criança como um ponto final de aninhadas para principal e consolidar as alterações.

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

Para uma questão de brevidade neste exemplo, vamos não adicionou os pontos finais para os perfis subordinado ou ascendente.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemplo 2: Adicionar pontos finais aninhados utilizando AzureRmTrafficManagerEndpoint novo

Neste exemplo, vamos adicionar um perfil subordinado existente como um ponto final de aninhadas a um perfil existente do principal. O perfil é especificado utilizando os nomes de grupo de recursos e de perfil.

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>Atualizar um ponto final de Gestor de tráfego

Existem duas formas de atualizar um extremo Gestor de tráfego existente:

1. Obter o perfil do Gestor de tráfego utilizando Get-AzureRmTrafficManagerProfile, Atualize as propriedades de ponto final dentro o perfil e consolide as alterações utilizando AzureRmTrafficManagerProfile conjunto. Este método tem a vantagem de ser capaz de atualizar a mais do que um ponto final numa única operação.
2. Obter o ponto final do Gestor de tráfego utilizando Get-AzureRmTrafficManagerEndpoint, Atualize as propriedades de ponto final e consolide as alterações utilizando AzureRmTrafficManagerEndpoint conjunto. Este método é mais simples, uma vez que não requer a indexação para a matriz de pontos finais no perfil.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exemplo 1: Atualizar os pontos finais utilizando Get-AzureRmTrafficManagerProfile e AzureRmTrafficManagerProfile conjunto

Neste exemplo, vamos modificar a prioridade em dois pontos finais dentro de um perfil existente.

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exemplo 2: Atualizar um ponto final utilizando Get-AzureRmTrafficManagerEndpoint e AzureRmTrafficManagerEndpoint conjunto

Neste exemplo, vamos modificar a espessura de um único ponto final de um perfil existente.

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Activar e desactivar os pontos finais e perfis

Gestor de tráfego permite que os pontos finais individuais ser ativado ou desativado, assim como permitindo activar e desactivar dos perfis de todas.
Estas alterações podem ser efetuadas por introdução/atualizar/definição os recursos de ponto final ou perfil. Para simplificar estas operações comuns, estas também são suportadas através do cmdlets dedicados.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemplo 1: Activar e desactivar um perfil do Gestor de tráfego

Para permitir que um perfil do Gestor de tráfego, utilize AzureRmTrafficManagerProfile ativar. O perfil pode ser especificado utilizando um objeto de perfil. O objeto de perfil pode ser passado através do pipeline de ou ao utilizar o '-TrafficManagerProfile' parâmetro. Neste exemplo, vamos especificar o perfil, o nome do grupo de perfil e recursos.

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Para desativar um perfil do Gestor de tráfego:

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

O cmdlet desativar AzureRmTrafficManagerProfile solicita-lhe confirmação. Neste pedido pode suprimir utilizando o '-forçar ' parâmetro.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemplo 2: Activar e desactivar um ponto final de Gestor de tráfego

Para permitir que um ponto final de tráfego Gestor, utilize AzureRmTrafficManagerEndpoint ativar. Existem duas formas para especificar o ponto final

1. Utilizando um objeto de TrafficManagerEndpoint passado através do pipeline de ou utilizando o '-TrafficManagerEndpoint' parâmetro
2. Utilizar o nome do ponto final, tipo de ponto final, nome de perfil e nome do grupo de recursos:

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Da mesma forma, para desativar um ponto final de Gestor de tráfego:

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Tal como acontece com desativar-AzureRmTrafficManagerProfile, o cmdlet desativar AzureRmTrafficManagerEndpoint solicita-lhe confirmação. Neste pedido pode suprimir utilizando o '-forçar ' parâmetro.

## <a name="delete-a-traffic-manager-endpoint"></a>Eliminar um ponto final de Gestor de tráfego

Para remover os pontos finais individuais, utilize o cmdlet AzureRmTrafficManagerEndpoint remover:

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Este cmdlet solicita-lhe confirmação. Neste pedido pode suprimir utilizando o '-forçar ' parâmetro.

## <a name="delete-a-traffic-manager-profile"></a>Eliminar um perfil do Gestor de tráfego

Para eliminar um perfil do Gestor de tráfego, utilize o cmdlet AzureRmTrafficManagerProfile remover, especificando os nomes de grupo de recursos e de perfil:

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Este cmdlet solicita-lhe confirmação. Neste pedido pode suprimir utilizando o '-forçar ' parâmetro.

Também pode ser especificado o perfil a ser eliminada utilizando um objeto de perfil:

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Também pode ser encaminhada esta sequência:

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Próximos passos

[Gestor de tráfego de monitorização](traffic-manager-monitoring.md)

[Considerações sobre o desempenho tráfego Gestor](traffic-manager-performance-considerations.md)
