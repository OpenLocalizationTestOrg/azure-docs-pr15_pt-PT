<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>FAQ: inverso DNS para o seu endereço IP atribuído Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Quanto inverter custo de registos DNS?
Se gratuitos!  Não há custo adicional para registos DNS inverso ou consultas.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>Os registos DNS inverso para o meu endereço de IP público atribuído Azure resolverá da internet?
Sim. Uma vez definido a propriedade inversa de DNS para o endereço IP público, o Azure gere todas as delegações de DNS e zonas DNS necessárias para se certificar de que o registo DNS inverso resolve para todos os utilizadores de internet.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Será criado um registo DNS inverso predefinido para minha endereços IP público?
Não. DNS inversa é uma funcionalidade optar ativamente pela. Sem registos DNS inverso predefinição são criados se optar por não configurá-los.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>O que é o formato para o nome de domínio completamente qualificado (FQDN)?
FQDNs estão especificados na ordem reencaminhar e tem de ser terminadas por um ponto (por exemplo, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>O que acontece se as verificações de validação para DNS inversa que especificou falharem?
Onde verifica a validação de DNS inversa falhar, a operação de gestão do serviço irá falhar. Corrija o valor DNS inverso conforme necessário e tente novamente.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>Pode gerir DNS inversa do meu Web site Azure?
DNS inversa não é suportada para sites públicos do Azure. DNS inversa é suportada para máquinas virtuais do Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>Pode configurar vários inverso registos DNS para o meu endereço IP público?
Não. Azure suporta um único registo DNS inverso para cada endereço IP público. No entanto, cada endereço IP público podem ter os seus próprios registo DNS inverso.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>Pode configurar inverso registos DNS para um endereço IP público IPv6?
Não.  Neste momento, os registos DNS inverso são suportados para endereços de IP IPv4 públicos apenas.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>Posso configurar um registo DNS inverso para o meu endereço IP público sem ter um DomainNameLabel especificado?
Não. Para tirar partido inverso registos DNS para os seus endereços de IP público, tem de especificar a propriedade DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>Posso enviar e-mails a domínios externos a partir do meu services calcular Azure?
Não. [Calcular azure services não suportam enviar e-mails para domínios externos](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
