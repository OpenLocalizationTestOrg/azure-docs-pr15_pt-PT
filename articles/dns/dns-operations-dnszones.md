<properties
   pageTitle="Gerir zonas de DNS através do PowerShell | Microsoft Azure"
   description="Pode gerir DNS zones através do Azure Powershell. Como atualizar, eliminar e criar zonas de DNS em Azure DNS"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="how-to-manage-dns-zones-using-powershell"></a>Como gerir DNS Zones através do PowerShell

> [AZURE.SELECTOR]
- [Clip Azure](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



Este artigo mostrar-lhe como gerir o seu DNS zone utilizando o PowerShell. Para que possa utilizar estes passos, terá de instalar a versão mais recente dos cmdlets do PowerShell de Gestor de recursos do Azure (1.0 ou posteriores). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="create-a-new-dns-zone"></a>Criar uma nova zona DNS

Para criar uma zona de DNS, consulte o artigo [criar uma zona DNS ao utilizar o PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Obter uma zona de DNS

Para obter uma zona de DNS, utilize o `Get-AzureRmDnsZone` cmdlet. Esta operação devolve um objeto de zona DNS correspondente a uma zona existente no Azure DNS. O objeto contiver dados sobre a zona (como o número de conjuntos de registos), mas não contém os conjuntos de registo próprios.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>Zonas DNS de lista

Por omitir o nome da zona de `Get-AzureRmDnsZone`, pode enumerar todas as zonas num grupo de recursos. Esta operação devolve uma matriz de objetos de zona.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>Atualizar uma zona de DNS

Alterações a um recurso de zona DNS podem ser efetuadas utilizando `Set-AzureRmDnsZone`. Isto não atualiza o qualquer um dos conjuntos de registos de DNS dentro da zona (consulte [como gerir DNS records](dns-operations-recordsets.md)). Só é utilizado para atualizar as propriedades do recurso zona própria. Este é atualmente limitado para o Azure Gestor de recursos etiquetas para o recurso de zona. Consulte o artigo [Etags e etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

Utilize um dos seguintes duas formas de atualizar a zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especificar a zona utilizando o grupo de nome e recursos de zona

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>Especificar a zona utilizando um objeto de $zone

Especificar a zona utilizando um objeto de $zone a partir do `Get-AzureRmDnsZone`. Quando utilizar `Set-AzureRmDnsZone` com um objecto $zone, Etag verificações serão utilizadas para garantir que não são substituídas alterações em simultâneo. Pode utilizar o opcional *-Substituir* mudar para suprimir estes controlos. Consulte o artigo [Etags e etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>Eliminar uma zona de DNS

Zonas de DNS podem ser eliminadas utilizando o cmdlet AzureRmDnsZone remover.

Antes de eliminar uma zona DNS no Azure DNS, terá de eliminar todos os conjuntos de registos, exceto os registos NS e SOA na raiz da zona que foram criados automaticamente quando a zona foi criada.

Utilize uma das seguintes duas formas para remover uma zona de DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especificar a zona com o nome da zona e o nome do grupo de recursos

Esta operação tem opcional *-Forçar* parâmetro que suprime o pedido para confirmar que pretende remover a zona de DNS.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>Especificar a zona utilizando um objeto de $zone

Especificar a zona utilizando um objeto de $zone a partir do `Get-AzureRmDnsZone`. Esta operação tem opcional *-Forçar* parâmetro que suprime o pedido para confirmar que pretende remover a zona de DNS. À semelhança `Set-AzureRmDnsZone`, especificar o horário utilizando um objeto de $zone ativa verificações de Etag para se certificar de alterações em simultâneo não são eliminadas. <BR>
Opcional *-Substituir* sinalizador suprime estes controlos. Consulte o artigo [Etags e etiquetas](dns-getstarted-create-dnszone.md#Etags-and-tags) para obter mais informações.

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



O objeto de zona também pode ser encaminhado em vez de a ser transmitida como um parâmetro:

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>Próximos passos

Depois de criar uma zona de DNS, crie [registos e conjuntos de registo](dns-getstarted-create-recordset.md) para iniciar a resolução de nomes do seu domínio da Internet.