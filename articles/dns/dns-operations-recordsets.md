<properties
   pageTitle="Gerir conjuntos de registos de DNS e registos utilizando o portal do Azure | Microsoft Azure"
   description="Gerir registos no Azure DNS e conjuntos de registos de DNS quando o alojamento do seu domínio no Azure DNS. Todos os comandos do PowerShell para operações nos conjuntos de registos e nos registos."
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

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Gerir os registos DNS e conjuntos de registos utilizando o PowerShell



> [AZURE.SELECTOR]
- [Portal do Azure](dns-operations-recordsets-portal.md)
- [Clip Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Este artigo mostra-lhe como gerir conjuntos de registos e registos para o seu horário DNS ao utilizar o Windows PowerShell.

É importante compreender a diferença entre os conjuntos de registos de DNS e os registos DNS individuais. Um conjunto de registo é o conjunto de registos numa zona que têm o mesmo nome e são do mesmo tipo. Para mais informações, consulte o artigo [registos utilizando o portal do Azure e conjuntos de registos DNS de criar](dns-getstarted-create-recordset-portal.md).

Para gerir os seus conjuntos de registos e registos, tem a versão mais recente dos cmdlets do PowerShell do Azure Gestor de recursos. Para mais informações, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Para mais informações sobre como trabalhar com o PowerShell, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).



## <a name="create-a-new-record-set-and-a-record"></a>Criar um novo conjunto de registo e um registo

Para criar um conjunto de registos por através do PowerShell, consulte o artigo [registos através do PowerShell e conjuntos de registos DNS de criar](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Obter um conjunto de registos

Para obter um conjunto de registos existente, utilize `Get-AzureRmDnsRecordSet`. Especifique o registo em definir nome relativo, o tipo de registo e a zona.

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

À semelhança `New-AzureRmDnsRecordSet`, o nome do registo tem de ser um nome relativo, ou seja, tem de excluir o nome da zona.

Pode especificar a zona utilizando o nome da zona e o nome do grupo de recursos ou utilizando um objeto de zona:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`Devolve um objecto local que representa o conjunto de registo que foi criado no Azure DNS.

## <a name="list-record-sets"></a>Conjuntos de registos de lista

Também pode utilizar`Get-AzureRmDnsRecordSet` aos conjuntos de registo de lista se omitir o *– nome* e/ou *– Tiporegisto* parâmetros.

### <a name="to-list-all-record-sets"></a>A lista todos os conjuntos de registos

Este exemplo devolve todos os conjuntos de registos, independentemente do nome ou tipo de registo:

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>Para conjuntos de registo de lista de um determinado tipo de registo

Este exemplo devolve todos os conjuntos de registos que correspondem ao tipo de registo determinado. Neste caso, o registo definir seja devolvido é "Um" registos:

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Pode ser especificada a zona utilizando um dos *Nome da zona de –* *– ResourceGroupName* parâmetros e (como mostrado) ou ao especificar um objeto de zona:

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>Adicionar um registo a um conjunto de registos

Adicionar registos para conjuntos de registos, utilizando o `Add-AzureRmDnsRecordConfig` cmdlet. Esta é uma operação offline. Apenas o objecto local que representa o conjunto de registos é alterado.

Os parâmetros para adicionar registos para um conjunto de registos variam consoante o tipo de conjunto de registos. Por exemplo, ao utilizar um conjunto de registo de tipo de "A", pode apenas especificar registos com o parâmetro *-Endereçoipv4*.

Registos adicionais podem ser adicionados a cada conjunto de registos por chamadas adicionais para `Add-AzureRmDnsRecordConfig`. Pode adicionar até 20 registos para qualquer conjunto de registos. No entanto, conjuntos de registos do tipo "CNAME" podem conter no máximo de um registo e um conjunto de registos não pode conter dois registos idênticos. Conjuntos de registos vazios (com os registos do zero) podem ser criados, mas não aparecem nos servidores de nomes Azure DNS.

Depois do conjunto de registos contém a coleção pretendida de registos, tem de consolidá-lo ao utilizar o `Set-AzureRmDnsRecordSet` cmdlet. Depois de um conjunto de registos ter sido consolidado, este substitui o registo existente definir no Azure DNS.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Para criar um registo a configurou com um único registo

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações para criar um registo também pode ser *encaminhada*, que significa que a verificação ser efetuada com o objeto de conjunto de registos ao utilizar o encaminhamento, em vez de prisma-lo como um parâmetro. Por exemplo:

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>Exemplos de tipo de registo adicionais

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modificar conjuntos de registos existentes

Os passos para modificar um conjunto de registos existente são semelhantes aos passos ao criar registos. A sequência de operações é da seguinte forma:

1.  Obter o registo existente definir utilizando `Get-AzureRmDnsRecordSet`.

2.  Modificar o conjunto de registos por adicionar os registos, remover registos, alterar os parâmetros de registo ou alterar o registo defina de time to live (TTL). Esta é uma operação offline. Apenas o objecto local que representa o conjunto de registos é alterado.

3.  Consolidar as alterações ao utilizar o `Set-AzureRmDnsRecordSet` cmdlet. Isto substitui o registo existente definir no Azure DNS.


### <a name="to-update-a-record-in-an-existing-record-set"></a>Para atualizar um registo de um conjunto de registos existente

Neste exemplo, vamos alterar o endereço IP de um "Um" registo existente:

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

O `Set-AzureRmDnsRecordSet` cmdlet utiliza etag verificações para se certificar de que não são substituídas alterações em simultâneo. Utilizar o *-Substituir* Sinalizar para suprimir estes controlos. Para mais informações, consulte o artigo [sobre etags e etiquetas](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Para modificar um registo SOA

Não pode adicionar ou remover registos do criado automaticamente SOA conjunto de registos no vértice do zona (nome = "@"). No entanto, pode modificar qualquer um dos parâmetros dentro de registo de SOA (exceto "anfitrião") e o registo definido TTL.

O exemplo seguinte mostra como alterar a propriedade de *correio eletrónico* do registo de SOA:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Para modificar os registos NS no vértice do zona

Não pode adicionar, para remover ou modificar os registos a criado automaticamente NS conjunto de registos no vértice do zona (nome = "@"). A única alteração que é permitida é modificar o conjunto de registos TTL.

O exemplo seguinte mostra como alterar a propriedade TTL do conjunto de registos do NS:

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>Para adicionar registos a um conjunto de registos existente

Neste exemplo, vamos adicionar dois registos MX adicionais para o conjunto de registos existente:

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>Remover um registo a partir de um conjunto de registos existente

Registos podem ser removidos de um registo definir utilizando `Remove-AzureRmDnsRecordConfig`. O registo que está a ser removido tem de ser uma correspondência exata com um registo existente em todos os parâmetros. Alterações tem de ser consolidadas utilizando `Set-AzureRmDnsRecordSet`.

Remover o último registo de um conjunto de registos não elimina o conjunto de registos. Consulte o artigo [Eliminar um conjunto de registos](#delete-a-record-set) abaixo para obter mais informações.


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

A sequência de operações para remover um registo a partir de um conjunto de registos pode ser também encaminhada, que significa que a verificação ser efetuada com o objeto de conjunto de registos ao utilizar o encaminhamento, em vez de prisma-lo como um parâmetro. Por exemplo:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Remover um registo AAAA de um conjunto de registos

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>Remover um registo CNAME de um conjunto de registos

Uma vez que um conjunto de registos CNAME pode conter no máximo de um registo, remover esse registo deixa um conjunto de registos vazio.

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>Remover um registo MX de um conjunto de registos

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>Remover um registo NS do conjunto de registos

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>Remover um registo SRV de um conjunto de registos

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>Remover um registo TXT de um conjunto de registos

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>Eliminar um conjunto de registos

Conjuntos de registos podem ser eliminados utilizando a `Remove-AzureRmDnsRecordSet` cmdlet. Não é possível eliminar a SOA e registo NS define no vértice do zona (nome = "@") que foram criados automaticamente quando a zona foi criada. Serão eliminados automaticamente se a zona é eliminada.

Utilize um dos seguintes três métodos para remover um conjunto de registos:

### <a name="specify-all-the-parameters-by-name"></a>Especificar todos os parâmetros pelo nome

Opcional *-Forçar* parâmetro pode ser utilizado para suprimir o pedido de confirmação.

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Especifique o conjunto de registos por nome e tipo e, especifique a zona ao objeto

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>Especificar o conjunto de registos por objeto

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Quando especificar o conjunto utilizando um objeto de registos, permite-etag verificações para se certificar de que não são eliminadas alterações em simultâneo. Opcional *-Substituir* sinalizador suprime estes controlos. Consulte o artigo [Etags e etiquetas](dns-getstarted-create-dnszone.md#tagetag) para obter mais informações.

Também pode ser encaminhado o objeto de conjunto de registos em vez de a ser transmitida como um parâmetro:

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Azure DNS, consulte o artigo [Descrição geral do Azure DNS](dns-overview.md). Para obter informações sobre como automatizar DNS, consulte o artigo [Criar DNS zones e registo conjuntos utilizando o SDK .NET](dns-sdk.md).

Para obter mais informações sobre inversa registos DNS, consulte [como gerir inverso registos DNS para os seus serviços através do PowerShell](dns-reverse-dns-record-operations-ps.md).
