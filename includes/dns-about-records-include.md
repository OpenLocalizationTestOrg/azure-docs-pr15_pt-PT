## <a name="about-records"></a>Acerca de registos

Cada registo DNS tem um nome e um tipo de. Registos estão organizados em vários tipos de acordo com os dados que contêm. O tipo mais comum é um "Um" registo, que liga um nome para um endereço de IPv4. Outro tipo é um registo de "MX", que liga um nome a um servidor de correio.

Azure DNS suporta todos os comuns tipos de registo DNS, incluindo A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT. Tenha em atenção que:
- Conjuntos de registos SOA são criados automaticamente com cada zona, não é possível criar separadamente.
- Registos SPF deverão ser criados utilizando o tipo de registo TXT. Para obter mais informações, consulte [esta página](http://tools.ietf.org/html/rfc7208#section-3.1).

No Azure DNS, os registos são especificados utilizando nomes relativos. Um nome de domínio "completamente qualificado" (FQDN) inclui o nome da zona, Considerando que não tem um nome de "relativo". Por exemplo, o nome do registo "www" na zona "contoso.com" relativo-lhe o nome do registo completamente qualificado www.contoso.com.

## <a name="about-record-sets"></a>Acerca de conjuntos de registos

Por vezes, tem de criar mais do que um registo DNS com um nome e tipo fornecido. Por exemplo, suponha que o "www.contoso.com" web site está alojado no dois endereços IP diferentes. O Web site necessita de dois diferentes um registos, uma para cada endereço IP. Este é um exemplo de um conjunto de registos:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS gere registos DNS utilizando os conjuntos de registos. Um conjunto de registo é o conjunto de registos DNS numa zona que têm o mesmo nome e são do mesmo tipo. A maior parte dos conjuntos de registos contenham um único registo, mas exemplos deste género, nos quais um conjunto de registos contém mais do que um registo, não são antigas.

Conjuntos de registos SOA e CNAME são exceções. Os padrões DNS não permitirem múltiplos registos com o mesmo nome para estes tipos.

A hora a dinâmicos ou o TTL, especifica quanto tempo cada registo é colocada em cache pelos clientes antes de voltar a ser consultados. Neste exemplo, o TTL é 3600 segundos ou 1 hora. O valor TTL especificado para o conjunto de registos, não para cada registo, para que o mesmo valor é utilizado para todos os registos dentro desse conjunto de registos.

#### <a name="wildcard-record-sets"></a>Conjuntos de registo de caracteres universais

Azure DNS suporta [registos de caracteres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Estes são devolvidos para qualquer consulta com um nome correspondente (a menos que não existe uma correspondência mais próxima a partir de um conjunto de registos que não sejam universal). Conjuntos de registos de caracteres universais são suportados para todos os tipos de registo exceto NS e SOA.  

Para criar um conjunto de registos de caracteres universais, utilize o nome do conjunto de registos "\*". Em alternativa, utilize um nome com a etiqueta "\*", por exemplo,"\*foo".

#### <a name="cname-record-sets"></a>Conjuntos de registos CNAME

Conjuntos de registos CNAME não é possível coexistência com outros conjuntos de registos com o mesmo nome. Por exemplo, não é possível criar um registo CNAME definido com o nome relativo "www" e um registo com o nome relativo "www" ao mesmo tempo. Uma vez que o vértice zona (nome = ‘@’) contém sempre NS e SOA gravam conjuntos que foram criados quando a zona foi criada, não é possível criar um registo CNAME definir no vértice do zona. Destas restrições surgirem a partir de padrões DNS e não são limitações da Azure DNS.
