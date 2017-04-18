Uma zona de DNS é utilizada para os registos DNS para um domínio específico do anfitrião. Para iniciar o alojamento do seu domínio, tem de criar uma zona DNS. Qualquer registo DNS que criou para um determinado domínio irá estar dentro de uma zona DNS do domínio. 

Por exemplo, o domínio "contoso.com" poderá conter um número de registos DNS, tais como "mail.contoso.com" (para um servidor de correio) e "www.contoso.com" (para um web site). 


## <a name="names"></a>Sobre nomes de zona DNS
 
- O nome da zona tem de ser exclusivo dentro do grupo de recursos e a zona não tem já existir. Caso contrário, a operação irá falhar.

- O mesmo nome de zona pode ser reutilizado um grupo de recursos diferente ou uma subscrição do Azure diferente. 

- Sempre que múltiplas zonas partilham o mesmo nome, será atribuída cada instância de endereços do servidor de nome diferente e pode ser delegada apenas uma instância do domínio principal. Para mais informações, consulte o artigo [delegado um domínio ao Azure DNS](../articles/dns/dns-domain-delegation.md).