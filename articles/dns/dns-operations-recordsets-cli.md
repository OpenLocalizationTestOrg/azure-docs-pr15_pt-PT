<properties
   pageTitle="Gerir conjuntos de registos de DNS e os registos DNS de Azure utilizando o clip do Azure | Microsoft Azure"
   description="Gerir registos no Azure DNS e conjuntos de registos de DNS quando o alojamento do seu domínio no Azure DNS. Todos os comandos de clip para operações de nos conjuntos de registos e nos registos."
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# <a name="manage-dns-records-and-record-sets-by-using-cli"></a>Gerir os registos DNS e conjuntos de registos, utilizando o clip


> [AZURE.SELECTOR]
- [Portal do Azure](dns-operations-recordsets-portal.md)
- [Clip Azure](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


Este artigo mostra-lhe como gerir conjuntos de registos e registos para o seu DNS zone, utilizando a interface de linha de comandos Azure em diferentes plataformas (CLI).

É importante compreender a diferença entre os conjuntos de registos de DNS e os registos DNS individuais. Um conjunto de registo é um conjunto de registos numa zona que têm o mesmo nome e são do mesmo tipo. Para mais informações, consulte o artigo [registos e conjuntos de registos de compreender](dns-getstarted-create-recordset-cli.md).


## <a name="configure-the-cross-platform-azure-cli"></a>Configurar o clip de Azure em diferentes plataformas

Azure DNS é um serviço de só de Gestor de recursos do Azure. Não tem uma API de gestão de serviço do Azure. Certifique-se de que o clip do Azure está configurado para utilizar o modo de Gestor de recursos, utilizando o `azure config mode arm` comando.

Se vir **erro: 'dns' não é um comando azure**, é provável que uma vez que está a utilizar o Azure clip no modo de gestão de serviço do Azure, não está no modo de Gestor de recursos.

## <a name="create-a-new-record-set-and-record"></a>Criar um novo conjunto de registos e registo

Para criar um novo registo definir no portal do Azure, consulte o artigo [criar um conjunto de registos e registos](dns-getstarted-create-recordset-cli.md).


## <a name="retrieve-a-record-set"></a>Obter um conjunto de registos

Para obter um conjunto de registos existente, utilize `azure network dns record-set show`. Especificar o grupo de recursos, o nome da zona, o registo definido relativa nome e tipo de registo. Utilize o exemplo abaixo, substituir os valores com a sua própria.

    azure network dns record-set show myresourcegroup contoso.com www A


## <a name="list-record-sets"></a>Conjuntos de registos de lista

Pode listar todos os registos numa zona de DNS utilizando o `azure network dns record-set list` comando. Tem de especificar o nome do grupo de recursos e o nome da zona.

### <a name="to-list-all-record-sets"></a>A lista todos os conjuntos de registos

Este exemplo devolve todos os conjuntos de registos, independentemente do nome ou tipo de registo:

    azure network dns record-set list myresourcegroup contoso.com

### <a name="to-list-record-sets-of-a-given-type"></a>A lista registo conjuntos de um determinado tipo

Este exemplo devolve todos os conjuntos de registos que correspondem ao tipo de registo determinado (neste caso, "Um" registos):

    azure network dns record-set list myresourcegroup contoso.com A


## <a name="add-a-record-to-a-record-set"></a>Adicionar um registo a um conjunto de registos

Adicionar registos para conjuntos de registos, utilizando o `azure network dns record-set add-record`comando. Os parâmetros para adicionar registos para um conjunto de registos variam consoante o tipo do registo que está a ser definido. Por exemplo, quando utiliza um conjunto de registo de tipo de "A", pode apenas especificar registos com o parâmetro `-a <IPv4 address>`.

Para criar um conjunto de registos, utilize o `azure network dns record-set create`comando. Especificar o grupo de recursos, o nome da zona, o registo definir nome relativo, tipo de registo e hora para live (TTL). Se o `--ttl` parâmetro não está definido, o valor (em segundos) assume a predefinição de quatro.

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


Depois de criar o conjunto de registos "Um", adicione o endereço de IPv4 utilizando o `azure network dns record-set add-record`comando.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


Os exemplos seguintes mostram como criar um conjunto de registo de cada tipo de registo. Cada conjunto de registos contém um único registo.

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## <a name="update-a-record-in-a-record-set"></a>Atualizar um registo num conjunto de registos

### <a name="to-add-another-ip-address-1234-to-an-existing-a-record-set-www"></a>Para adicionar outro endereço IP (1.2.3.4) a um "Um" registo existente defina ("www"):

    azure network dns record-set add-record  myresourcegroup contoso.com  A
    -a 1.2.3.4
    info:    Executing command network dns record-set add-record
    Record set name: www
    + Looking up the dns zone "contoso.com"
    + Looking up the DNS record set "www"
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/a
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:        IPv4 address                : 192.168.1.1
    data:        IPv4 address                : 1.2.3.4
    data:
    info:    network dns record-set add-record command OK

### <a name="to-remove-an-existing-value-from-a-record-set"></a>Para remover um valor existente a partir de um conjunto de registos
Utilizar `azure network dns record-set delete-record`.

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
    info:    Executing command network dns record-set delete-record
    + Looking up the DNS record set "www"
    Delete DNS record? [y/n] y
    + Updating DNS record set "www"
    data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
    data:    Name                            : www
    data:    Type                            : Microsoft.Network/dnszones/A
    data:    Location                        : global
    data:    TTL                             : 4
    data:    A records:
    data:    IPv4 address                    : 192.168.1.1
    data:
    info:    network dns record-set delete-record command OK



## <a name="remove-a-record-from-a-record-set"></a>Remover um registo a partir de um conjunto de registos

Registos podem ser removidos de um registo definir utilizando `azure network dns record-set delete-record`. O registo que está a ser removido tem de ser uma correspondência exata com um registo existente em todos os parâmetros.

Remover o último registo de um conjunto de registos não elimina o conjunto de registos. Para mais informações, consulte a secção deste artigo denominado [Eliminar um conjunto de registos](#delete).

    azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

    azure network dns record-set delete myresourcegroup contoso.com www A

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Remover um registo AAAA de um conjunto de registos

    azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### <a name="remove-a-cname-record-from-a-record-set"></a>Remover um registo CNAME de um conjunto de registos

    azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### <a name="remove-an-mx-record-from-a-record-set"></a>Remover um registo MX de um conjunto de registos

    azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### <a name="remove-an-ns-record-from-record-set"></a>Remover um registo NS do conjunto de registos

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### <a name="remove-a-ptr-record-from-a-record-set"></a>Remover um registo PTR de um conjunto de registos
Neste caso ' meu-arpa-zone.com' representa o horário ARPA que representa o intervalo de IP.  Cada registo PTR definir nesta zona corresponde ao endereço IP neste intervalo de IP.

    azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### <a name="remove-an-srv-record-from-a-record-set"></a>Remover um registo SRV de um conjunto de registos

    azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### <a name="remove-a-txt-record-from-a-record-set"></a>Remover um registo TXT de um conjunto de registos

    azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>Eliminar um conjunto de registos

Conjuntos de registos podem ser eliminados utilizando a `Remove-AzureRmDnsRecordSet` cmdlet. Não é possível eliminar a SOA e registo NS define no vértice do zona (nome = "@") que foram criados automaticamente quando a zona foi criada. Serão eliminados automaticamente se a zona é eliminada.

No exemplo seguinte, o registo "A" definir "teste a" será removido da zona DNS "contoso.com":

    azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

O parâmetro opcional *- q* pode ser utilizado para suprimir o pedido de confirmação.


## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o Azure DNS, consulte o artigo [Descrição geral do Azure DNS](dns-overview.md). Para obter informações sobre como automatizar DNS, consulte o artigo [Criar DNS zones e registo conjuntos utilizando o SDK .NET](dns-sdk.md).

Se pretender trabalhar com registos DNS inverso, consulte o artigo [como gerir inverso registos DNS para os seus serviços utilizando o clip do Azure](dns-reverse-dns-record-operations-cli.md).
