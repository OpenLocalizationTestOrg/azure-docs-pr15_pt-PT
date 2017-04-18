<properties
   pageTitle="Criar uma zona DNS com o clip | Microsoft Azure"
   description="Saiba como criar zonas de DNS para o Azure DNS passo a passo para iniciar o alojamento do seu domínio DNS utilizando clip"
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-an-azure-dns-zone-using-cli"></a>Criar uma zona de Azure DNS utilizando o clip


> [AZURE.SELECTOR]
- [Portal do Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Clip Azure](dns-getstarted-create-dnszone-cli.md)


Este artigo irá guiá-lo através dos passos para criar uma zona de DNS com clip. Também pode criar uma zona DNS utilizando o PowerShell ou o portal do Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## <a name="before-you-begin"></a>Antes de começar

Estas instruções utilizam clip do Microsoft Azure. Certifique-se de que Atualize para a mais recente clip Azure (0.9.8 ou posterior) para utilizar os comandos do Azure DNS. Tipo de `azure -v` para verificar que versão do Azure clip está atualmente instalado no seu computador.

## <a name="step-1---set-up-azure-cli"></a>Passo 1 - configurar o clip do Azure

### <a name="1-install-azure-cli"></a>1. instalar clip Azure

Pode instalar o Azure clip para Windows, Linux ou Mac. Os passos seguintes tem de ser concluídos antes de poder gerir DNS Azure utilizando o clip do Azure. Obter mais informações estão disponíveis em [instalar o clip do Azure](../xplat-cli-install.md). Os comandos DNS exigem Azure clip versão 0.9.8 ou posterior.

Todos os comandos do fornecedor de rede no clip podem ser encontrado utilizando o seguinte comando:

    azure network

### <a name="2-switch-cli-mode"></a>2. modo de clip de mudar de

Azure DNS utiliza Gestor de recursos do Azure. Certifique-se de que mudar o modo de clip para utilizar comandos de processador.

    azure config mode arm

### <a name="3-sign-in-to-your-azure-account"></a>3. iniciar sessão sua conta do Azure

Será pedido para autenticar com as suas credenciais. Tenha em atenção que só pode utilizar contas ORGID.

    azure login -u "username"

### <a name="4-select-the-subscription"></a>4. Selecione a subscrição

Escolha as suas subscrições Azure para utilizar.

    azure account set "subscription name"

### <a name="5-create-a-resource-group"></a>5. Crie um grupo de recursos

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. É utilizada como a localização predefinida para recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são global, não regionais, a escolha da localização do grupo de recursos não tem impacto no Azure DNS.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

    azure group create -n myresourcegroup --location "West US"


### <a name="6-register"></a>6. registo de

O serviço do Azure DNS é gerido pelo fornecedor de recurso Microsoft.Network. A sua subscrição Azure tem de estar registado para utilizar este fornecedor de recursos antes de poder utilizar Azure DNS. Esta é uma operação única para cada subscrição.

    azure provider register --namespace Microsoft.Network


## <a name="step-2---create-a-dns-zone"></a>Passo 2 - criar uma zona de DNS

Uma zona de DNS é criada utilizando o `azure network dns zone create` comando. Opcionalmente, pode criar uma zona DNS juntamente com etiquetas. Etiquetas são uma lista de pares valor do nome e são utilizadas pelo Gestor de recursos do Azure para recursos de etiqueta para fins de agrupamento ou de faturaçãohttps. Para mais informações acerca das etiquetas, consulte o artigo [utilizar etiquetas para organizar os recursos do Azure](../resource-group-using-tags.md).

No Azure DNS, os nomes de zona devem ser especificados sem uma pôr termo **'.'**. Por exemplo, como '**contoso.com**' em vez de '**contoso.com.**'.


### <a name="to-create-a-dns-zone"></a>Para criar uma zona de DNS

O exemplo abaixo cria uma zona DNS chamada *contoso.com* no grupo de recursos denominado *MyResourceGroup*.

Utilize o exemplo para criar o seu DNS zone, substituindo os valores para a sua própria.

    azure network dns zone create myresourcegroup contoso.com

### <a name="to-create-a-dns-zone-and-tags"></a>Para criar uma zona de DNS e etiquetas.

Azure DNS clip suporta etiquetas das zonas DNS especificadas utilizando o opcional *-etiqueta* parâmetro. O exemplo seguinte mostra como criar uma zona DNS com duas etiquetas, project = demonstração e envelope = teste.

Utilize o exemplo abaixo para criar uma zona de DNS e etiquetas, substituindo os valores para a sua própria.

    azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## <a name="view-records"></a>Ver registos

Também é criar uma zona de DNS cria os seguintes registos DNS:

- Registo de 'Iniciar de autoridade' (SOA). Este é presente na raiz da cada zona DNS.

- Os registos do servidor (NS) nome autoritativas. Mostram estas servidores de nomes que estiver a alojar a zona. Azure DNS utiliza um conjunto de servidores de nomes e os servidores de nomes tão diferentes podem ser atribuídos a zonas de diferentes no Azure DNS. Para mais informações, consulte [delegado um domínio ao Azure DNS](dns-domain-delegation.md) .

Para ver estes registos, utilize `azure network dns-record-set show`.<BR>
*Utilização: conjunto de registos de dns de rede show <-grupo de recursos >< dns zone nome--> <name><type>*


No exemplo abaixo, se executar o comando com recurso grupo *myresourcegroup*, o registo definir nome *"@"* (para um registo de raiz) e escreva *SOA*, -lo será lucro o seguinte resultado:


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
Para ver os registos NS criados com a zona, utilize o seguinte comando:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Conjuntos de registo na raiz (ou *vértice*) de uma utilização DNS Zone **@** como o registo em definir nome.

## <a name="test"></a>Teste

Pode testar o seu DNS zone utilizando ferramentas DNS, tal como o nslookup, ESCAVAR, ou o `Resolve-DnsName` cmdlet do PowerShell.

Se ainda não ainda delegado o domínio ao utilizar a nova zona no Azure DNS, tem de direcionar a consulta DNS diretamente para um dos servidores de nomes para o seu horário. Os servidores de nomes para o seu horário são constantes os registos do NS, como por "conjunto de registos de dns de rede azure Mostrar" acima indicado. Certifique-se a SUBST os valores corretos para o seu horário no comando abaixo.

O exemplo seguinte utiliza ESCAVAR para consultar o domínio contoso.com utilizar os servidores de nomes atribuídos da zona de DNS. A consulta tem que apontar para um servidor de nomes para o qual utilizado * @ * e nome de zona utilizando ESCAVAR.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>Próximos passos

Depois de criar uma zona de DNS, crie [registos e conjuntos de registo](dns-getstarted-create-recordset-cli.md) para iniciar a resolução de nomes do seu domínio da Internet.
