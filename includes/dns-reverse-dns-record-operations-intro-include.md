## <a name="what-is-reverse-dns"></a>O que é o DNS inversa?

Registos DNS convencionais ativar um mapeamento a partir de um nome DNS (tal como 'www.contoso.com') para um endereço IP (como 64.4.6.100).  DNS inversa permite a tradução de um endereço IP (64.4.6.100) voltar a um nome ('www.contoso.com').

Registos DNS inverso são utilizados em várias situações. Por exemplo, os registos DNS inverso amplamente são utilizados na luta contra spam de e-mail, verificando o remetente da mensagem de e-mail.  Servidor de receção de correio irá obter o registo DNS inverso de endereço IP do servidor de envio e verifique se o anfitrião está autorizado para enviar um e-mail a partir do domínio de origem. (Tenha em atenção no entanto esse [Azure calcular services não suportam os domínios externos enviar e-mails](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Inversa como funciona o DNS

Registos DNS inverso estão alojados em especiais zonas DNS, conhecidas como zonas de 'ARPA'.  Estas zonas formam uma hierarquia DNS separada em paralelo com a hierarquia normal alojamento domínios como 'contoso.com'.

Por exemplo, o DNS record 'www.contoso.com' é implementada utilizando um registo DNS 'A' com o nome 'www' na zona 'contoso.com.  Este registo aponta para o endereço IP correspondente, neste caso 64.4.6.100.  A pesquisa inversa é implementada separadamente, utilizando um registo de 'PTR' com o nome '100' na zona '6.4.64.in-addr.arpa' (Repare que os endereços IP são revertidos em zonas ARPA).  Este registo PTR, se tiver sido configurado corretamente, aponta para o nome 'www.contoso.com.

Quando uma organização é atribuída um bloco de endereços IP, também adquirir o direito de gerirem a zona ARPA correspondente. As zonas ARPA correspondente para os blocos de endereços IP utilizados pelo Azure são alojadas e geridas pelo Microsoft. O seu ISP pode alojar a zona ARPA para os seus endereços IP para si ou pode permitir a zona ARPA num serviço DNS da sua escolha, tal como Azure DNS do anfitrião.

>[AZURE.NOTE] Reencaminhar pesquisas DNS e inversa pesquisas DNS são implementadas em separado, paralelas hierarquias DNS. A pesquisa inversa para 'www.contoso.com' é **não** alojado na zona 'contoso.com', preferir estão alojados na zona ARPA para o bloco de endereços IP correspondente.

Para mais informações sobre o DNS inversa, consulte o artigo [Pesquisa inversa de DNS](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Azure suporte para DNS inversa

Azure suporta dois cenários separados relativas para inverter DNS:

1. Que aloja a zona ARPA correspondente ao seu bloco de endereços IP.
2. Permitindo-lhe configurar o registo DNS inverso para o endereço IP associado ao seu serviço Azure.

Para suportar o DNS antigo, Azure pode ser utilizados para alojar zonas do ARPA e gerir os registos PTR para cada pesquisa inversa de DNS.  O processo de criação da zona ARPA, a delegação, instalação e configuração do registos PTR é o mesmo que para regulares zonas DNS.  São as diferenças apenas que tem de ser configurada a delegação através do seu ISP em vez da entidade de registo DNS e apenas o tipo de registo PTR deverá ser utilizado.

Para suportar o último, o Azure permite-lhe configurar a pesquisa inversa para os endereços IP atribuída no seu serviço.  Esta pesquisa inversa é configurada Azure como um registo PTR na zona ARPA correspondente.  Estas zonas ARPA correspondentes a todos os intervalos IP utilizados pelo Azure, estão alojadas pela Microsoft. **O resto deste artigo descreve este cenário em detalhe.**
