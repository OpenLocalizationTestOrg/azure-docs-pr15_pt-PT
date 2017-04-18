<properties 
   pageTitle="Zonas de DNS e registos | Microsoft Azure" 
   description="Descrição geral de suporte para alojamento de DNS zones e registos DNS da Microsoft Azure." 
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
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>Zonas de DNS e registos

Esta página explica os conceitos de chave de domínios, zonas de DNS e os registos DNS e conjuntos de registos e como são suportados no Azure DNS.

## <a name="domain-names"></a>Nomes de domínio
 
O sistema de nomes de domínio é uma hierarquia de domínios. A hierarquia começa a partir do domínio 'raiz', cujo nome é simplesmente**.**.  Por baixo desta vir domínios de nível superior, tal como 'com», «líquido», «organograma», 'uk' ou 'jp'.  Abaixo estes são os domínios de segundo nível, tais como 'org.uk' ou 'co.jp'. Os domínios na hierarquia de DNS são distribuídos globalmente, alojado pelos servidores de nomes DNS em todo o mundo.

Uma entidade de registo de nome de domínio é uma organização que permite-lhe comprar um nome de domínio, tal como 'contoso.com'.  Comprar um nome de domínio dá-lhe à direita para controlar a hierarquia DNS nesse nome, por exemplo permitindo-lhe direcionar o nome 'www.contoso.com' ao seu web site da empresa. A entidade de registo pode alojar o domínio na suas própria os servidores de nomes em seu nome ou em alternativa pode especificar os servidores de nomes alternativo.

O DNS Azure fornece uma infraestrutura de servidor de nome globalmente distribuído e disponibilidade de alta, que pode utilizar para alojar o seu domínio. Por que aloja os seus domínios no Azure DNS, pode gerir os seus registos DNS com as mesmas credenciais, APIs, ferramentas, faturação e suporte como dos outros serviços Azure.

Azure DNS não suporta atualmente comprar dos nomes de domínio. Se quiser comprar um nome de domínio, terá de utilizar uma entidade de registo de nome de domínio de terceiros. A entidade de registo de normalmente irá cobrar uma pequena taxa anual. Os domínios, em seguida, podem ser alojados no Azure DNS para a gestão de registos DNS. Consulte o artigo [delegado um domínio ao Azure DNS](dns-domain-delegation.md) para obter detalhes.

## <a name="dns-zones"></a>Zonas de DNS 

Uma zona de DNS é utilizada para os registos DNS para um domínio específico do anfitrião. Para iniciar o alojamento do seu domínio no Azure DNS, precisa de criar uma zona DNS para esse nome de domínio. Cada registo DNS do seu domínio, em seguida, é criado dentro esta zona de DNS. 

Por exemplo, o domínio 'contoso.com' pode conter vários registos DNS, tais como mail.contoso.com (para um servidor de correio) e www.contoso.com (para um web site).

Ao criar uma zona de DNS no Azure DNS, o nome da zona tem de ser exclusivo dentro do grupo de recursos. O mesmo nome de zona pode ser reutilizado um grupo de recursos diferente ou uma subscrição do Azure diferente. Sempre que múltiplas zonas partilham o mesmo nome, cada instância está atribuída endereços do servidor de nome diferente. Pode ser configurado apenas um conjunto de endereços com a entidade de registo de nome de domínio.

>[AZURE.NOTE] Não possui um nome de domínio para criar uma zona de DNS com esse nome de domínio no Azure DNS o proprietário. No entanto, tem de ser proprietário do domínio para configurar os servidores de nomes Azure DNS como os servidores de nomes correto para o nome de domínio com a entidade de registo de nome de domínio.

Para mais informações, consulte o artigo [delegado um domínio ao Azure DNS](dns-domain-delegation.md).

## <a name="dns-records"></a>Registos DNS

### <a name="record-types"></a>Tipos de registo

Cada registo DNS tem um nome e um tipo de. Registos estão organizados em vários tipos de acordo com os dados que contêm. O tipo mais comum é um "A" registo, que liga um nome para um endereço de IPv4. Outro tipo comum é um registo de 'MX', que liga um nome a um servidor de correio.

Azure DNS suporta todos os tipos de registo DNS comuns: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

### <a name="record-names"></a>Nomes de registos

No Azure DNS, os registos são especificados utilizando nomes relativos. Um nome de domínio *completamente qualificado* (FQDN) inclui o nome da zona, Considerando que não tem um nome *relativa* . Por exemplo, o nome do registo relativo www na zona 'contoso.com' dá o nome do registo completamente qualificado 'www.contoso.com'.

Um *vértice* de registo é um registo DNS na raiz (ou *vértice*) uma zona de DNS. Por exemplo, na zona DNS 'contoso.com', um registo vértice também tem o nome totalmente qualificado 'contoso.com' (isto é, por vezes chamado um domínio *livre* ).  Por convenção, o nome relativo '@' é utilizado para criar registos de vértice.

### <a name="record-sets"></a>Conjuntos de registos
Por vezes, tem de criar mais do que um registo DNS com um nome e tipo fornecido. Por exemplo, suponha que o web site da 'www.contoso.com' está alojado no dois endereços IP diferentes. O Web site necessita de dois diferentes um registos, uma para cada endereço IP. Este é um exemplo de um conjunto de registos:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS faz a gestão dos registos DNS através de *conjuntos de registo*. Um conjunto de registos (também conhecido como um registo conjunto de *recursos* ) é o conjunto de registos DNS numa zona que têm o mesmo nome e são do mesmo tipo. A maior parte dos conjuntos de registos contenham um único registo, mas exemplos como este um, em que um conjunto de registos contém mais do que um registo, não são antigas.

Por exemplo, suponha que já tiver criado um registo "www" na zona 'contoso.com', apontar para o período de inquérito endereço '134.170.185.46' (o primeiro registo acima).  Para criar o segundo registo qual seria adicionar esse registo para o conjunto de registos existente, em vez de criar um novo conjunto de registo.

Os tipos de registo SOA e CNAME são exceções. Os padrões DNS não permitirem múltiplos registos com o mesmo nome para estes tipos, pelo que estes conjuntos de registos só podem conter um único registo.

### <a name="time-to-live"></a>Time to live

A hora a dinâmicos ou o TTL, especifica quanto tempo cada registo é colocada em cache pelos clientes antes de voltar a ser consultados. No exemplo acima, o TTL é 3600 segundos ou 1 hora.

No Azure DNS, o valor TTL especificado para o conjunto de registos, não para cada registo, para que o mesmo valor é utilizado para todos os registos dentro desse conjunto de registos.  Pode especificar qualquer valor TTL entre 1 e 2.147.483.647 segundos.

### <a name="wildcard-records"></a>Registos de caracteres universais

Azure DNS suporta [registos de caracteres universais](https://en.wikipedia.org/wiki/Wildcard_DNS_record). São devolvidos registos de caracteres universais em resposta ao qualquer consulta com um nome correspondente (a menos que não existe uma correspondência mais próxima a partir de um conjunto de registos que não sejam universais). Conjuntos de registos de caracteres universais são suportados para todos os tipos de registo exceto NS e SOA.  

Para criar um conjunto de registos de caracteres universais, utilize o nome do conjunto de registos '\*'. Em alternativa, também pode utilizar um nome com '\*'como respectiva mais à esquerda etiqueta, por exemplo,'\*foo '.

### <a name="cname-records"></a>Registos CNAME

Conjuntos de registos CNAME não é possível coexistência com outros conjuntos de registos com o mesmo nome. Por exemplo, não é possível criar um registo CNAME definido com o nome relativo 'www' e um registo com o nome relativo 'www' ao mesmo tempo.

Uma vez que o vértice zona (nome = ‘@’) contém sempre NS e SOA gravam conjuntos que foram criados quando a zona foi criada, não é possível criar um registo CNAME definir no vértice do zona.

Destas restrições surgirem a partir de padrões DNS e não são limitações da Azure DNS.

### <a name="ns-records"></a>Registos NS

Um conjunto de registos do NS é criado automaticamente no vértice de cada zona (nome = ‘@’), e eliminados automaticamente quando é eliminada a zona (não podem ser eliminada separadamente).  Pode modificar o TTL deste conjunto de registos, mas não é possível modificar os registos que são pré-configurada para consultar os servidores de nomes Azure DNS atribuídos à zona.

Pode criar e eliminar outros registos NS dentro da zona, não no vértice do zona.  Esta opção permite-lhe configurar zonas subordinadas (consulte [delegar subdomínios no Azure DNS](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>Registos de SOA

Um conjunto de registos SOA é criado automaticamente no vértice de cada zona (nome = ‘@’), e eliminados automaticamente quando é eliminada a zona.  Registos de SOA não podem ser criados ou eliminados separadamente. 

Pode modificar todas as propriedades do registo de SOA exceto a propriedade 'host', que é pré-configurada para consultar o nome do servidor de nome primário fornecido pela Azure DNS.

### <a name="spf-records"></a>Registos SPF

Registos de política Framework (SPF) remetente são utilizadas para especificar quais os servidores de e-mail são permitidos para enviar e-mails em nome de um nome de domínio determinado.  Configuração correta de registos SPF é importante para evitar que os destinatários marcar o seu correio eletrónico como 'não solicitado'.

DNS RFCs originalmente introduzido um novo tipo de registo de 'SPF' para suportam este cenário. Para suportar os servidores de nomes mais antigos, estes também permitiam a utilização do tipo de registo TXT para especificar os registos SPF.  Este ambiguidade por um instrutor para confusões, que foram resolvido por [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Este facto mencionado que registos SPF só deverão ser criados utilizando o tipo de registo TXT e preterida o tipo de registo de SPF. 

**Registos SPF são suportados pelo Azure DNS e devem ser criados com o tipo de registo TXT.** O tipo de registo SPF obsoleto não é suportado. Quando [Importar um DNS ficheiro de zona](dns-import-export.md), os registos SPF com o tipo de registo SPF são convertidas no tipo de registo TXT. 

### <a name="srv-records"></a>Registos SRV

[Registos SRV](https://en.wikipedia.org/wiki/SRV_record) são utilizados pelos diferentes serviços para especificar as localizações do servidor. Quando especificar um registo SRV no Azure DNS:

- *Service* e *protocol* tem de ser especificados como parte do nome do conjunto de registos, o prefixo sublinhado.  Por exemplo, '\_sip. \_tcp.name'.  Para um registo no vértice do zona, não é necessário para especificar '@' o nome do registo, simplesmente utilizar a service e protocol, por exemplo, '\_sip. \_tcp'.
- A *prioridade*, *peso*, *porta*e *destino* estão especificados como parâmetros de cada registo no conjunto de registos. 

## <a name="tags-and-metadata"></a>Etiquetas e metadados

### <a name="tags"></a>Etiquetas
Etiquetas são uma lista de pares valor do nome em são utilizadas pelo Gestor de recursos do Azure para recursos de etiqueta.  Gestor de recursos do Azure utiliza etiquetas para permitir que as vistas filtradas da sua fatura Azure e também permite-lhe definir uma política que são necessárias etiquetas. Para mais informações acerca das etiquetas, consulte o artigo [utilizar etiquetas para organizar os recursos do Azure](../resource-group-using-tags.md).

Azure DNS suporta a utilizar o Gestor de recursos do Azure etiquetas no recursos de zona DNS.  Não suporta etiquetas no conjuntos de registos de DNS.

### <a name="metadata"></a>Metadados

Como alternativa às etiquetas de conjunto de registos, Azure DNS suporta anotar conjuntos registos utilizando 'metadados'.  Semelhantes às etiquetas, metadados permite-lhe associar pares valor do nome de cada conjunto de registos.  Isto pode ser útil, por exemplo para o registo a finalidade de cada conjunto de registos.  Ao contrário de etiquetas, metadados não podem ser utilizados para fornecer uma vista filtrada da sua fatura Azure e não podem ser especificado numa política de Gestor de recursos do Azure.

## <a name="limits"></a>Limites

Os limites predefinidos seguintes aplicam-se através de Azure DNS:

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>Próximos passos

- Para começar a utilizar o Azure DNS, saiba como [criar uma zona de DNS](dns-getstarted-create-dnszone-portal.md) e [criar registos DNS](dns-getstarted-create-recordset-portal.md).
- Para migrar uma zona existente de DNS, saiba como [Importar e ficheiro de zona DNS](dns-import-export.md).
