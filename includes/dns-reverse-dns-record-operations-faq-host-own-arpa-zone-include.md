<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>FAQ: que aloja o seu horário ARPA no Azure DNS

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>Pode alojar zonas ARPA para os meus blocos IP atribuído ISP no Azure DNS?
Sim. Alojamento das zonas ARPA para o seus próprio intervalos IP no Azure DNS é totalmente suportada.

Basta [criar a zona no Azure DNS](dns-getstarted-create-dnszone.md), em seguida, trabalhar com o seu ISP para [Delegar a zona](dns-domain-delegation.md).  Em seguida, pode gerir os registos PTR para cada pesquisa inversa da mesma forma como outros tipos de registo.

Também pode [Importar uma zona de pesquisa inversa existente utilizando o clip do Azure](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Quanto o alojamento meus custos de zona ARPA?
Que aloja a zona ARPA para o seu IP ISP atribuído bloco no Azure DNS é cobrado à [taxas fixas do Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>Pode alojar zonas ARPA para os endereços IPv4 e IPv6 no Azure DNS?
Sim.