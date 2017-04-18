#### <a name="create-an-a-record-set-with-single-record"></a>Criar um registo a configurou com registo único

Para criar um conjunto de registos, utilize `azure network dns record-set create`. Especificar o grupo de recursos, o nome da zona, o registo definir nome relativo, tipo de registo e hora para live (TTL).

    azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

Depois de criar o A gravar conjunto, adicione o endereço IPv4 para o registo em conjunto com `azure network dns record-set add-record`.

    azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1

#### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Criar um registo AAAA definido com um único registo

    azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

#### <a name="create-a-cname-record-set-with-a-single-record"></a>Criar um registo CNAME definido com um único registo

Registos CNAME permitirem apenas um valor de cadeia única.


    azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300

    azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"


#### <a name="create-an-mx-record-set-with-a-single-record"></a>Criar um registo MX definido com um único registo

Neste exemplo, vamos utilizar o nome do conjunto de registos "@" para criar o registo MX no vértice do zona (neste caso, "contoso.com"). Este é comuns de registos MX.

    azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

    azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


#### <a name="create-an-ns-record-set-with-a-single-record"></a>Criar um registo NS definido com um único registo

    azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300

    azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com"

#### <a name="create-a-ptr-record-set-with-a-single-record"></a>Criar um registo PTR definido com um único registo  
Neste caso ' meu-arpa-zone.com' representa o horário ARPA que representa o intervalo de IP.  Cada registo PTR definir nesta zona corresponde ao endereço IP neste intervalo de IP.    

    azure network dns record-set add-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"   

#### <a name="create-an-srv-record-set-with-a-single-record"></a>Criar um registo SRV definido com um único registo

Se estiver a criar um registo SRV na raiz da zona, pode especificar "_service" e "_protocol" no nome do registo. Não é necessário para incluir "@" no nome do registo.


    azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com"

#### <a name="create-a-txt-record-set-with-single-record"></a>Criar um registo TXT configurou com registo único

    azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

    azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record"
