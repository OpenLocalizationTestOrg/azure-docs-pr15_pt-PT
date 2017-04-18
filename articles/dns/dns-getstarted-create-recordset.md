<properties
   pageTitle="Criar um conjunto de registos e registos para uma zona DNS através do PowerShell | Microsoft Azure"
   description="Como criar registos do anfitrião de Azure DNS. Configurar o registo conjuntos e registos através do PowerShell"
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



# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>Criar conjuntos de registos de DNS e registos através do PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Clip Azure](dns-getstarted-create-recordset-cli.md)

Este artigo explica o processo de criação de registos e conjuntos de registos ao utilizar o Windows PowerShell. Depois de criar o seu DNS zone, adicione os registos DNS para o seu domínio. Para executar esta tarefa, tem primeiro compreender os registos DNS e conjuntos de registos.

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>Verifique se tem a versão mais recente do PowerShell

Certifique-se de que instalou a versão mais recente dos cmdlets do PowerShell do Azure Gestor de recursos. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

## <a name="create-a-record-set-and-record"></a>Criar um conjunto de registos e registo

Esta secção descreve como criar um conjunto de registos e registo.


### <a name="1-connect-to-your-subscription"></a>1. a ligar à sua subscrição

Abra o seu consola do PowerShell e ligar à sua conta. Utilize o exemplo seguinte para ajudá-lo ligar:

    Login-AzureRmAccount

Verifique as subscrições para a conta.

    Get-AzureRmSubscription

Especifique a subscrição que pretende utilizar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

Para mais informações sobre como trabalhar com o PowerShell, consulte o artigo [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).


### <a name="2-create-a-record-set"></a>2. Crie um conjunto de registos

Pode criar conjuntos de registos, utilizando o `New-AzureRmDnsRecordSet` cmdlet. Ao criar um conjunto de registos, tem de especificar que o registo do conjunto de nome, a zona, a time to live (TTL) e o tipo de registo.

Para criar um conjunto de registos num vértice da zona (neste caso, "contoso.com"), utilize o nome do registo "@", incluindo as aspas. Esta é uma comuns Convenção DNS.

O exemplo seguinte cria um conjunto de registos com o nome relativo "www" na zona de DNS "contoso.com". O nome completamente qualificado do conjunto de registo é "www.contoso.com". O tipo de registo é "A", e o valor TTL é 60 segundos. Depois de concluir este passo, terá de um conjunto de registo "www" branco que é atribuído a variável *$rs*.

    $rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60

#### <a name="if-a-record-set-already-exists"></a>Se um registo definir já existe

Se existir um registo já definido, o comando falhar, a menos que a *-Substituir* parâmetro é utilizado. O *-Substituir* opção accionadores um pedido de confirmação, pode suprimir utilizando o *-Forçar* mudar.


    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]


Neste exemplo, especifique a zona utilizando o nome da zona e o nome do grupo de recursos. Em alternativa, pode especificar um objeto de zona, como devolvido pela `Get-AzureRmDnsZone` ou `New-AzureRmDnsZone`.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
    $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

`New-AzureRmDnsRecordSet`Devolve um objecto local que representa o conjunto de registo que foi criado no Azure DNS.

### <a name="3-add-a-record"></a>3. Adicionar um registo

Para utilizar o conjunto de registos recentemente criado "www", é necessário adicionar registos para o mesmo. Pode adicionar IPv4 *registos para o registo "www"* definir utilizando o exemplo seguinte. Este exemplo baseia-se na variável *$rs* definida por si no passo anterior.

Adicionar registos a um registo definir utilizando `Add-AzureRmDnsRecordConfig` é uma operação offline. Apenas o local variável *$rs* é atualizado.


    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221

### <a name="4-commit-the-changes"></a>4. consolidar as alterações

Consolide as alterações para o conjunto de registos. Utilizar `Set-AzureRmDnsRecordSet` para carregar as alterações para o conjunto de DNS Azure de registos.

    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="5-retrieve-the-record-set"></a>5. obter o conjunto de registos

Pode obter o conjunto a partir do Azure DNS através da utilização de registos `Get-AzureRmDnsRecordSet` conforme mostrado no seguinte exemplo.


    Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


Também pode utilizar a ferramenta nslookup ou outras ferramentas DNS para o novo conjunto de registos de consulta.

Se ainda não tiver delegados o domínio para os servidores de nomes Azure DNS, terá de especificar explicitamente o nome, o servidor e o endereço da sua zona.


    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>Criar um conjunto de registo de cada tipo com um único registo


Os exemplos seguintes mostram como criar um conjunto de registo de cada tipo de registo. Cada conjunto de registos contém um único registo.

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]


## <a name="next-steps"></a>Próximos passos

[Como gerir DNS zones através do PowerShell](dns-operations-dnszones.md)

[Gerir os registos DNS e conjuntos de registos utilizando o PowerShell](dns-operations-recordsets.md)

[Automatizar operações Azure com .NET SDK](dns-sdk.md)
