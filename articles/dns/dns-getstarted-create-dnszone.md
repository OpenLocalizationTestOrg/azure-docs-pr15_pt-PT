<properties
   pageTitle="Introdução ao Azure DNS | Microsoft Azure"
   description="Saiba como criar zonas de DNS para o Azure DNS. Este é um passo a passo para obter a sua primeira zona DNS criada para iniciar o alojamento do seu domínio DNS através do PowerShell."
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

# <a name="create-a-dns-zone-using-powershell"></a>Criar uma zona DNS através do Powershell

> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Clip Azure](dns-getstarted-create-dnszone-cli.md)

Este artigo irá guiá-lo através dos passos para criar uma zona de DNS ao utilizar o PowerShell. Também pode criar uma zona DNS utilizando clip ou o portal do Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Sobre Etags e etiquetas

### <a name="etags"></a>Etags

Suponha que duas pessoas ou dois processos tentam modificar um registo DNS ao mesmo tempo. Aquele wins? E é que o vencedor sabe que apenas tenha substituído alterações criadas por outra pessoa?

Azure DNS utiliza Etags para processar alterações em simultâneo ao mesmo recurso em segurança. Cada recurso DNS (zona ou conjunto de registos) tem um Etag associada. Sempre que um recurso é obtido, o respetivo Etag também serem recuperado. Ao atualizar um recurso, tem a opção para passar novamente o Etag por isso, Azure DNS pode verificar que a Etag as correspondências de servidor. Uma vez que cada atualização para um recurso que resulte na Etag a gerar, um erro de correspondência Etag indica que ocorreu uma alteração em simultâneo. Etags também são utilizadas quando cria um novo recurso para se certificar de que o recurso ainda não existe.

Por predefinição, o Azure DNS PowerShell utiliza Etags bloquear alterações em simultâneo para as zonas e que grave conjuntos. Opcional *-Substituir* mudar podem ser utilizados para suprimir Etag verificações, nesse caso quaisquer alterações em simultâneo que tenham ocorrido será substituído.

Nível do Azure DNS REST API, Etags são especificadas utilizando os cabeçalhos de HTTP.  É dado o comportamento da tabela seguinte:

|Cabeçalho|Comportamento|
|------|--------|
|Nenhum|COLOCAR sempre é concluída com êxito (sem verificações Etag)|
|Se CORRESP<etag>|COLOQUE apenas é concluída com êxito se existe recurso e corresponde ao Etag|
|Se CORRESP *     | COLOQUE apenas é concluída com êxito se existir do recurso|
|Se nenhum-correspondência * |  COLOQUE apenas é concluída com êxito se não existir recurso|

### <a name="tags"></a>Etiquetas

Etiquetas são diferentes dos Etags. Etiquetas são uma lista de pares valor do nome e são utilizadas pelo Gestor de recursos do Azure para recursos de etiqueta para fins de agrupamento ou de faturaçãohttps. Para mais informações acerca das etiquetas, consulte o artigo [utilizar etiquetas para organizar os recursos do Azure](../resource-group-using-tags.md).

Azure DNS PowerShell suporta etiquetas em zonas e conjuntos de registos especificados utilizando as opções de `-Tag` parâmetro.


## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem os seguintes itens antes de iniciar a configuração.

- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Terá de instalar a versão mais recente dos cmdlets do PowerShell de Gestor de recursos do Azure (1.0 ou posteriores). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

## <a name="step-1---sign-in"></a>Passo 1 - início de sessão

Abra o seu consola do PowerShell e ligar à sua conta. Para mais informações, consulte o artigo [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).

Utilize o exemplo seguinte para ajudá-lo ligar:

    Login-AzureRmAccount

Verifique as subscrições para a conta.

    Get-AzureRmSubscription

Especifique a subscrição que pretende utilizar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>Passo 2 - criar um grupo de recursos

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. É utilizada como a localização predefinida para recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são global, não regionais, a escolha da localização do grupo de recursos não tem impacto no Azure DNS.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>Passo 3 - Register

O serviço do Azure DNS é gerido pelo fornecedor de recurso Microsoft.Network. A sua subscrição Azure tem de estar registado para utilizar este fornecedor de recursos antes de poder utilizar Azure DNS. Esta é uma operação única para cada subscrição.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>Passo 4 - criar uma zona DNS

É criada uma zona DNS utilizando o `New-AzureRmDnsZone` cmdlet. Existem exemplos abaixo para criar uma zona DNS com ou sem etiquetas. Para mais informações sobre etiquetas, consulte a secção no [etiquetas](#tags) neste artigo.

>[AZURE.NOTE] No Azure DNS, os nomes de zona devem ser especificados sem uma pôr termo **'.'**. Por exemplo, como '**contoso.com**' em vez de '**contoso.com.**'.

### <a name="to-create-a-dns-zone"></a>Para criar uma zona de DNS

O exemplo abaixo cria uma zona DNS chamada *contoso.com* no grupo de recursos denominado *MyResourceGroup*. Utilize o exemplo para criar uma zona DNS, substituindo os valores para a sua própria.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>Para criar uma zona de DNS com etiquetas

O exemplo seguinte mostra como criar uma zona de DNS com duas etiquetas, *projeto = demonstração* e *Envelope = teste*. Utilize o exemplo para criar uma zona DNS, substituindo os valores para a sua própria.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>Ver registos

Também é criar uma zona de DNS cria os seguintes registos DNS:

- O registo de *Início da autoridade* (SOA). Este é presente na raiz da cada zona DNS.

- Os registos do servidor (NS) nome autoritativas. Mostram estas servidores de nomes que estiver a alojar a zona. Azure DNS utiliza um conjunto de servidores de nomes e os servidores de nomes tão diferentes podem ter sido eleito para diferentes zonas no Azure DNS. Consulte o artigo [Delegar um domínio ao Azure DNS](dns-domain-delegation.md) para obter mais informações.

Para ver estes registos, utilize `Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


Conjuntos de registo na raiz (ou *vértice*) de uma utilização DNS Zone **@** como o registo em definir nome.


## <a name="test"></a>Teste

Pode testar o seu DNS zone utilizando ferramentas DNS como nslookup, escavar ou o [cmdlet do PowerShell NomeDNS resolver](https://technet.microsoft.com/library/jj590781.aspx).

Se ainda não ainda delegada o domínio ao utilizar a nova zona no Azure DNS, terá de direcionar a consulta DNS diretamente para um dos servidores de nomes para o seu horário. Os servidores de nomes para o seu horário são dadas nos registos NS, tal como listada pelo `Get-AzureRmDnsRecordSet` acima. Certifique-se a SUBST os valores corretos para o seu horário para o comando abaixo.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>Próximos passos

Depois de criar uma zona de DNS, crie [registos e conjuntos de registo](dns-getstarted-create-recordset.md) para iniciar a resolução de nomes do seu domínio da Internet.

