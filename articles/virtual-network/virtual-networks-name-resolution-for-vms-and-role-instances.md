<properties 
   pageTitle="Resolução VMs e instâncias de função"
   description="Cenários de resolução de nomes para Azure IaaS, soluções híbrido, entre serviços em nuvem diferente, do Active Directory e utilizar o seu próprio servidor DNS "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Resolução de nomes para VMs e instâncias de função

Dependendo de como utilizar o Azure para alojar IaaS, PaaS e soluções híbrido, poderá ter permitir os VMs e instâncias de funções que criar para comunicar com os outros. Apesar desta comunicação pode ser efetuada ao utilizar endereços IP, é muito mais simples utilizar nomes que podem ser lembrados facilmente e não são alterados. 

Quando precisar de instâncias de função e VMs alojados no Azure resolver os nomes de domínio para endereços IP internos, pode de utilizar um de dois métodos:

- [Resolução do nome do Azure fornecido pela](#azure-provided-name-resolution)

- [Resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server) (que poderá reencaminhar consultas para os servidores DNS Azure fornecido pela) 

O tipo de resolução do nome que utiliza depende como seu VMs e instâncias de função precisam de comunicar com os outros.

**A tabela seguinte ilustra cenários e soluções de resolução do nome correspondente:**

| **Cenário** | **Solução** | **Sufixo** |
|--------------|--------------|----------|
| Resolução do nome entre instâncias de função ou VMs localizadas na mesma serviço na nuvem ou rede virtual | [Resolução do nome do Azure fornecido pela](#azure-provided-name-resolution)| nome do anfitrião ou um FQDN |
| Resolução do nome entre instâncias de função ou VMs localizados na redes virtuais diferentes | Cliente com gestão os servidores DNS reencaminhamento de consultas entre vnets para a resolução por Azure (DNS proxy).  consulte o artigo [resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server)| FQDN apenas |
| Resolução de nomes do computador e o serviço no local a partir de instâncias de função ou VMs no Azure | Cliente com gestão servidores DNS (controlador de domínio, por exemplo, no local, controlador de domínio só de leitura local ou num DNS secundário sincronizado utilizando as transferências de zona).  Consulte o artigo [resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server)|FQDN apenas |
| Resolução de nomes de anfitriões Azure de computadores no local | Reencaminhar consultas para um servidor de proxy DNS com gestão de cliente na vnet correspondente, o servidor proxy reencaminha consultas para o Azure para a resolução. Consulte o artigo [resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server)| FQDN apenas |
| Inverter DNS para o IPs interno | [Resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server) | n/d |
| Resolução do nome entre VMs ou instâncias de função localizadas na serviços em nuvem diferente, não na rede virtual| Não aplicável. Conectividade entre instâncias de função nos serviços em nuvem diferente e VMs não é suportada fora da rede virtual.| n/d |



## <a name="azure-provided-name-resolution"></a>Resolução do nome do Azure fornecido pela

Juntamente com a resolução de nomes DNS públicos, o Azure fornece resolução do nome interna para VMs e instâncias de função que se encontram dentro do mesmo rede virtual ou serviço na nuvem.  VMs/instâncias num serviço de nuvem partilhe o mesmo sufixo DNS (para que o nome do anfitrião sozinho é suficiente) mas na nuvem diferente clássica redes virtual serviços tem sufixos DNS diferentes para que o FQDN é necessário para resolver nomes entre serviços em nuvem diferente.  Redes virtuais no modelo de implementação de Gestor de recursos, o sufixo de DNS é consistente através da rede virtual (para que não é necessário o FQDN) e nomes de DNS podem ser atribuídos a NIC e VMs. Apesar de resolução do nome do Azure fornecido pela não requer qualquer configuração, não é a escolha adequada para todos os cenários de implementação, conforme visto na tabela acima.

> [AZURE.NOTE] No caso de funções web e trabalhador, também pode aceder os endereços IP internos de instâncias de função com base na função nome e instância do número de utilizar a API resto do Azure serviço de gestão. Para mais informações, consulte o artigo [Referência da API do serviço de gestão de restantes](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### <a name="features-and-considerations"></a>Funcionalidades e considerações

**Funcionalidades:**

- Facilidade de utilização: não é necessária configuração para que possa utilizar resolução do nome do Azure fornecido pela.

- O serviço de resolução do nome do Azure fornecido pela é altamente disponível, poupando-lhe a necessidade de criar e gerir clusters dos seus servidores DNS.

- Pode ser utilizado em conjunto com o seus próprio servidores DNS para resolver os nomes de anfitriões no local tanto Azure.

- Resolução do nome é fornecida entre instâncias de função/VMs dentro do serviço na nuvem mesmo sem ser necessária para um FQDN.

- Resolução do nome é fornecida entre VMs em redes virtuais que utilizem o modelo de implementação do Gestor de recursos, sem ser necessária para o FQDN. Redes virtuais no modelo de implementação clássica requerem o FQDN quando resolução de nomes na serviços em nuvem diferente. 

- Pode utilizar nomes de anfitriões que melhor descrevem as implementações, em vez de trabalhar com nomes gerados automaticamente.

**Considerações sobre:**

- Não é possível modificar o sufixo de criado o Azure DNS.

- Não é possível registar manualmente os seus próprios registos.

- WINS e NetBIOS não são suportadas. (Não é possível ver os VMs no Explorador do Windows)

- Nomes de anfitriões têm de ser compatível com o DNS (tem de utilizar apenas 0-9-z e '-' e não pode iniciar ou terminar com um '-'. Consulte RFC 3696 secção 2.)

- Tráfego de consulta DNS é limitado para cada VM. Isto não deve ter um impacto na maior parte das aplicações.  Se pedido limitação se observar, certifique-se de que a cache do lado do cliente está activada.  Para obter mais detalhes, consulte o artigo [tirar o máximo partido da resolução de nomes do Azure fornecido pela](#Getting-the-most-from-Azure-provided-name-resolution).

- Apenas VMs no serviços na 180 nuvem primeiros estão registados para cada rede virtual num modelo de implementação clássica. Isto não se aplica a redes virtuais em modelos de implementação do Gestor de recursos.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obter o máximo partido da resolução do nome do Azure fornecido pela
**Colocação em cache do lado do cliente:**

Nem todas as consultas DNS tem de ser enviado através da rede.  Colocação em cache do lado do cliente ajuda a reduzir a latência e melhorar resistência para sinais de rede por resolver periódicas consultas de DNS a partir de uma cache local.  Registos DNS contêm um Time To Live (TTL) que lhe permite a cache armazenar o registo do tempo possível sem que afetam a última modificação da registo, pelo que a colocação em cache do lado do cliente é adequada para a maior parte das situações.

A predefinição cliente DNS do Windows tem uma cache DNS incorporada.  Algumas Linux distros não inclua a colocação em cache por predefinição, é recomendável que um ser adicionada a cada VM Linux (depois de verificar que não há uma cache local já).

Existem um número de diferentes DNS colocação em cache pacotes disponíveis, por exemplo, dnsmasq, Eis os passos para instalar o dnsmasq a distros mais comuns:

- **Ubuntu (utiliza resolvconf)**:
    - tal instale o pacote de dnsmasq ("sudo possam obter instalar dnsmasq").
- **SUSE (utiliza netconf)**:
    - Instale o pacote de dnsmasq ("sudo zypper instalar dnsmasq") 
    - Ativar o serviço de dnsmasq ("systemctl ativar dnsmasq.service") 
    - iniciar o serviço de dnsmasq ("systemctl iniciar dnsmasq.service") 
    - Editar "/ etc/sysconfig/rede/config" e alterar NETCONFIG_DNS_FORWARDER = "" para "dnsmasq"
    - Actualizar resolv.conf ("atualização netconfig") para definir a cache de como o resolver DNS local
- **OpenLogic (utiliza NetworkManager)**:
    - Instale o pacote de dnsmasq ("sudo yum instalar dnsmasq")
    - Ativar o serviço de dnsmasq ("systemctl ativar dnsmasq.service")
    - iniciar o serviço de dnsmasq ("systemctl iniciar dnsmasq.service")
    - Adicionar "anexar como prefixo domínio--os servidores de nomes 127.0.0.1;" para "/etc/dhclient-eth0.conf"
    - Reinicie o serviço de rede ("serviço rede reiniciar") para definir a cache de como o resolver DNS local

> [AZURE.NOTE] O pacote de 'dnsmasq' é apenas um dos muitos caches do DNS disponíveis para Linux.  Antes de o utilizar, verifique as sua conformidade para as suas necessidades e outros cache não está instalado.

**Número de tentativas do lado do cliente:**

O DNS é principalmente um protocolo UDP.  Como o protocolo UDP não garante a entrega da mensagem, lógica de repetição é resolvida o protocolo DNS propriamente dito.  Cada cliente DNS (sistema operativo) pode apresentar lógica de repetição diferente consoante a preferência dos criadores:

 - Sistemas operativos do Windows repetir depois de 1 em segundo lugar e, em seguida, novamente após a outra 2, 4 e outro 4 segundos. 
 - As predefinição Linux configuração repetições de após 5 segundos.  Recomenda-se para alterar esta opção para repetir 5 vezes em intervalos segunda 1.  

Para verificar as definições atuais num VM Linux, 'gato /etc/resolv.conf' e aspeto na linha de 'Opções', por exemplo:

    options timeout:1 attempts:5

O ficheiro resolv.conf é normalmente gerados automaticamente e não deve ser editado.  Os passos para adicionar a linha de 'Opções' específicos variam consoante distro:

- **Ubuntu** (utiliza resolvconf):
    - Adicione a linha de opções para ' / etc/resolveconf/resolv.conf.d/head' 
    - executar resolvconf -u para atualizar
- **SUSE** (utiliza netconf):
    - Adicionar 'timeout:1 tentativas: 5' para o NETCONFIG_DNS_RESOLVER_OPTIONS = "" parâmetro em '/ etc/sysconfig/rede/config' 
    - executar 'netconfig update' para atualizar
- **OpenLogic** (utiliza NetworkManager):
    - Adicionar 'eco "opções timeout:1 tentativas: 5" ' para ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
    - executar o reinício da rede de serviço para atualizar

## <a name="name-resolution-using-your-own-dns-server"></a>Resolução de nomes utilizando o seu servidor de DNS
Existem várias situações onde as suas necessidades de resolução de nome podem ir para além das funcionalidades fornecidas pelo Azure, por exemplo quando a utilizar domínios do Active Directory ou quando necessitar de resolução DNS entre redes virtuais (vnets).  Para cobrir estes cenários, Azure fornece a capacidade para utilizar o seus próprio servidores DNS.  

Os servidores DNS dentro de uma rede virtual podem reencaminhar consultas de DNS para resoluções de recursiva do Azure para resolver nomes de anfitriões dentro desse rede virtual.  Por exemplo, o controlador de ponto um domínio (CC) em execução no Azure pode responder a consultas de DNS para os seus domínios e reencaminhar todas as outras consultas para Azure.  Esta opção permite-VMs ver o recursos no local (através do Centro de dados) e o Azure fornecido pela nomes de anfitriões (através do reencaminhador).  Acesso a resoluções de recursiva do Azure é fornecido através do IP virtual 168.63.129.16.

Reencaminhamento de chamadas DNS também permite vnet, nomeadamente a resolução de DNS e permite que os seus computadores no local resolver os nomes de anfitriões Azure fornecido pela.  Para resolver hostname uma VM, o servidor de DNS VM tem de estar na mesma rede virtual e ser configurado para reencaminhar hostname consultas para Azure.  Como é diferente em cada vnet o sufixo de DNS, pode utilizar as regras de reencaminhamento condicional para enviar consultas de DNS para o vnet correta para a resolução.  A imagem seguinte mostra duas vnets e uma rede no local efetuar vnet, nomeadamente resolução DNS com este método.  Um reencaminhador de DNS de exemplo está disponível na [Galeria de modelos de guia de introdução do Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) e [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Vnet, nomeadamente DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Ao utilizar a resolução do nome Azure fornecido pela, um sufixo de DNS interno (*. internal.cloudapp.net) é fornecido para cada VM utilizando DHCP.  Isto permite resolução do nome do anfitrião conforme os registos de nome do anfitrião estão na zona internal.cloudapp.net.  Quando utiliza o seu próprio solução de resolução do nome, o sufixo IDNS não for fornecido para VMs porque-interfere com outras arquiteturas de DNS (como cenários de domínio).  Em vez disso, fornecemos-não está a funcionar marcador de posição (reddog.microsoft.com).  

Se for necessário, pode ser determinado o sufixo de DNS interno utilizando o PowerShell ou a API:

-  Para obter redes virtuais em modelos de implementação do Gestor de recursos, o sufixo está disponível através do recurso de [cartão de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou o cmdlet [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) .    
-  Em modelos de implementação clássico, está disponível através da chamada [Obter API de implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou o sufixo de [Get AzureVM-depurar](https://msdn.microsoft.com/library/azure/dn495236.aspx) cmdlet.


Se o reencaminhamento de consultas para Azure não conforme as suas necessidades, terá de fornecer o seu próprio solução DNS.  A solução de DNS será necessário:

-  Fornece resolução hostname adequado, por exemplo, através do [DDNS](virtual-networks-name-resolution-ddns.md).  Nota, se utilizar DDNS poderá ter de desativar a limpeza do registo de DNS, as concessões de DHCP do Azure muito longas e limpeza poderá remover prematuramente registos DNS. 
-  Fornece resolução recursiva adequado para permitir a resolução de nomes de domínio externo.
-  Ser acessível (TCP e UDP na porta 53) a partir dos clientes que serve e ser possível aceder à internet.
-  Protegida contra o acesso a partir da internet, a atenuar ameaças que pelos agentes externos.

> [AZURE.NOTE] Para um melhor desempenho, quando utiliza o Azure VMs como servidores DNS, devem ser desactivados IPv6 e um [Endereço IP público do nível da instância](virtual-networks-instance-level-public-ip.md) devem ser atribuídas a cada servidor DNS VM.  Se optar por utilizar o Windows Server como o seu servidor de DNS, [Este artigo](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fornece análise de desempenho adicionais e otimizações.


### <a name="specifying-dns-servers"></a>Especificar servidores DNS

Quando utilizar o seus próprio servidores DNS, o Azure fornece a capacidade de especificar várias servidores DNS por rede virtual ou por interface da rede (Gestor de recurso) / (clássico) do serviço em nuvem.  Os servidores DNS especificados para uma interface de serviço de rede nuvem obtém precedência sobre as especificadas para a rede virtual.

> [AZURE.NOTE] Propriedades de ligação de rede, tais como servidor DNS IPs, não deve ser editada diretamente no Windows VMs como poderá obter apagados durante o serviço heal quando o adaptador de rede virtual substituído. 


Ao utilizar o modelo de implementação do Gestor de recursos, os servidores DNS podem ser especificados no Portal, de API/modelos ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [nic](https://msdn.microsoft.com/library/azure/mt163668.aspx)) ou PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [nic](https://msdn.microsoft.com/library/mt619370.aspx)).

Ao utilizar o modelo de implementação clássico, os servidores DNS para a rede virtual podem ser especificados no Portal ou [o ficheiro de *Configuração de rede* ](https://msdn.microsoft.com/library/azure/jj157100).  Para serviços em nuvem, os servidores DNS estão especificados através de [um ficheiro de *Configuração do serviço* ](https://msdn.microsoft.com/library/azure/ee758710) ou no PowerShell ([AzureVM novo](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [AZURE.NOTE] Se alterar as definições de DNS para uma máquina de rede virtual/virtual que já é implementado, é necessário reiniciar a cada VM afetado para que as alterações sejam aplicadas.


## <a name="next-steps"></a>Próximos passos

Modelo de implementação do Gestor de recursos:

- [Criar ou atualizar uma rede virtual](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [Criar ou actualizar um cartão de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [Novo AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [Novo AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
Modelo de implementação clássico:

- [Esquema de configuração do serviço Azure](https://msdn.microsoft.com/library/azure/ee758710)
- [Esquema de configuração de rede virtual](https://msdn.microsoft.com/library/azure/jj157100)
- [Configurar uma rede Virtual utilizando um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md) 

