<properties 
   pageTitle="Opções de resolução do nome de DNS para Linux VMs no Azure"
   description="Serviços de DNS de cenários para Linux VMs no Azure IaaS, incluindo fornecidos de resolução de nome, servidor híbrido do externo DNS e trazer o próprio."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Opções de resolução do nome DNS para Linux VMs no Azure

Azure fornece resolução do nome de DNS por predefinição para todos os VMs contidos numa única rede Virtual. É possível implementar o seu próprio solução de resolução do nome DNS ao configurar o seus próprio serviços de DNS no seu VMs alojados Azure. Os cenários seguintes devem ajudar a escolher aquele funciona melhor para a sua situação específica.

- [Resolução do nome do Azure fornecido pela](#azure-provided-name-resolution)

- [Resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server) 

O tipo de resolução do nome que utiliza depende como seu VMs e instâncias de função precisam de comunicar com os outros.

**A tabela seguinte ilustra cenários e soluções de resolução do nome correspondente:**

| **Cenário** | **Solução** | **Sufixo** |
|--------------|--------------|----------|
| Resolução do nome entre instâncias de função ou VMs localizados na mesma rede virtual | [Resolução do nome do Azure fornecido pela](#azure-provided-name-resolution)| nome do anfitrião ou um FQDN |
| Resolução do nome entre instâncias de função ou VMs localizados na redes virtuais diferentes | Cliente com gestão os servidores DNS reencaminhamento de consultas entre vnets para a resolução por Azure (DNS proxy).  consulte o artigo [resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server)| FQDN apenas |
| Resolução de nomes do computador e o serviço no local a partir de instâncias de função ou VMs no Azure | Cliente com gestão servidores DNS (por exemplo, controlador de domínio no local, controlador de domínio só de leitura local ou num DNS secundário sincronizado utilizando as transferências de zona).  Consulte o artigo [resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server)|FQDN apenas |
| Resolução de nomes de anfitriões Azure de computadores no local | Reencaminhar consultas para um servidor de proxy DNS com gestão de cliente na vnet correspondente, o servidor proxy reencaminha consultas para o Azure para a resolução. Consulte o artigo [resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server)| FQDN apenas |
| Inverter DNS para o IPs interno | [Resolução de nomes utilizando o seu servidor de DNS](#name-resolution-using-your-own-dns-server) | n/d |

## <a name="azure-provided-name-resolution"></a>Resolução do nome do Azure fornecido pela

Juntamente com a resolução de nomes DNS públicos, o Azure fornece resolução do nome interna para VMs e instâncias de função que se encontram dentro da mesma rede virtual.  PROCESSADOR com base em redes virtuais, o sufixo de DNS é consistente através da rede virtual (para que não é necessário o FQDN) e nomes de DNS podem ser atribuídos a NIC e VMs. Apesar de resolução do nome do Azure fornecido pela não requer qualquer configuração, não é a escolha adequada para todos os cenários de implementação, conforme visto no tabela anterior.

### <a name="features-and-considerations"></a>Funcionalidades e considerações

**Funcionalidades:**

- Facilidade de utilização: não é necessária configuração para utilizar a resolução do nome do Azure fornecido pela.

- O serviço de resolução do nome do Azure fornecido pela é altamente disponível, poupando-lhe a necessidade de criar e gerir clusters dos seus servidores DNS.

- Pode ser utilizado juntamente com os seus servidores DNS para resolver os nomes de anfitriões no local tanto Azure.

- Resolução do nome é fornecida entre VMs em redes virtuais sem ser necessária para o FQDN. 

- Pode utilizar nomes de anfitriões que melhor descrevem as implementações, em vez de trabalhar com nomes gerados automaticamente.

**Considerações sobre:**

- Não é possível modificar o sufixo de criado o Azure DNS.

- Não é possível registar manualmente os seus próprios registos.

- WINS e NetBIOS não são suportadas.

- Nomes de anfitriões têm de ser compatível com o DNS (tem de utilizar apenas 0-9-z e '-' e não pode iniciar ou terminar com um '-'. Consulte RFC 3696 secção 2.)

- Tráfego de consulta DNS é limitado para cada VM. Isto não deve ter um impacto na maior parte das aplicações.  Se o pedido limitação for respeitado, certifique-se de que a cache do lado do cliente está activada.  Para mais informações, consulte o artigo [tirar o máximo partido da resolução de nomes do Azure fornecido pela](#Getting-the-most-from-Azure-provided-name-resolution).


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obter o máximo partido da resolução do nome do Azure fornecido pela
**Colocação em cache do lado do cliente:**

Nem todas as consultas DNS são enviada através da rede.  Colocação em cache do lado do cliente ajuda a reduzir a latência e melhorar resistência para sinais de rede por resolver periódicas consultas de DNS a partir de uma cache local.  Registos DNS contêm um Time To Live (TTL) que lhe permite a cache armazenar o registo do tempo possível sem que afetam a última modificação da registo.  Por esta razão, colocação em cache do lado do cliente é adequada para a maior parte das situações.

Algumas distros Linux não inclua a colocação em cache por predefinição.  É recomendável que adicionar uma para cada VM Linux (depois de verificar que não há uma cache local já).

Existem várias diferentes DNS colocação em cache pacotes disponíveis, por exemplo dnsmasq, Eis os passos para instalar o dnsmasq a distros mais comuns:

- **Ubuntu (utiliza resolvconf)**:
    - Instale o pacote de dnsmasq ("sudo possam get instalar dnsmasq").
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

> [AZURE.NOTE]: O pacote de 'dnsmasq' é apenas um dos muitos caches do DNS disponíveis para Linux.  Antes de utilizá-lo, verifique o seu conformidade para as suas necessidades e outros cache não está instalado.

**Número de tentativas do lado do cliente:**

O DNS é principalmente um protocolo UDP.  Como o protocolo UDP não garante a entrega da mensagem, lógica de repetição é resolvida o protocolo DNS propriamente dito.  Cada cliente DNS (sistema operativo) pode apresentar lógica de repetição diferente consoante a preferência dos criadores:

 - Sistemas operativos do Windows Repetir após um segundo lugar e, em seguida, novamente após a outra duas, quatro e outro quatro segundos. 
 - As predefinição Linux configuração repetições de depois de cinco segundos.  Deverá alterar esta opção para repetir cinco vezes em intervalos de um segundo.  

Para verificar as definições atuais num VM Linux, 'gato /etc/resolv.conf' e aspeto na linha de 'Opções', por exemplo:

    options timeout:1 attempts:5

O ficheiro de resolv.conf é gerados automaticamente e não deve ser editado.  Os passos para adicionar a linha de 'Opções' específicos variam consoante distro:

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
Existem várias situações onde as suas necessidades de resolução do nome poderão ir além as funcionalidades fornecidos pelo Azure, por exemplo quando necessitar de resolução DNS entre redes virtuais (vnets).  Para este cenário de folha de rosto, Azure fornece a capacidade para utilizar o seus próprio servidores DNS.  

Os servidores DNS dentro de uma rede virtual podem reencaminhar consultas de DNS para resoluções de recursiva do Azure para resolver nomes de anfitriões dentro desse rede virtual.  Por exemplo, um servidor de DNS em execução no Azure pode responder a consultas de DNS para o seus próprio ficheiros de zona DNS e reencaminhar todas as outras consultas para Azure.  Esta opção permite-VMs ver ambas as entradas nos seus ficheiros de zona e nomes de anfitriões Azure fornecido pela (através do reencaminhador).  Acesso a resoluções de recursiva do Azure é fornecido através do IP virtual 168.63.129.16.

Reencaminhamento de chamadas DNS também permite vnet, nomeadamente a resolução de DNS e permite que os seus computadores no local resolver os nomes de anfitriões Azure fornecido pela.  Para resolver hostname uma VM, o servidor de DNS VM tem de estar na mesma rede virtual e ser configurado para reencaminhar hostname consultas para Azure.  Como é diferente em cada vnet o sufixo de DNS, pode utilizar as regras de reencaminhamento condicional para enviar consultas de DNS para o vnet correta para a resolução.  A imagem seguinte mostra duas vnets e uma rede no local efetuar vnet, nomeadamente resolução DNS com este método:

![Vnet, nomeadamente DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Ao utilizar a resolução do nome do Azure fornecido pela, o sufixo de DNS interno é fornecido para cada VM utilizando DHCP.  Quando utiliza o seu próprio solução de resolução do nome, este sufixo não for fornecido para VMs porque-interfere com outras arquiteturas de DNS.  Para consultar as máquinas por FQDN ou para configurar o sufixo no seu VMs, pode ser determinado o sufixo utilizando o PowerShell ou a API:

-  Para gestão de recursos do Azure gerido vnets, o sufixo não está disponível através do recurso [placa de interface de rede](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou pode executar o comando `azure network public-ip show <resource group> <pip name>` para apresentar os detalhes do seu IP público, incluindo o FQDN do NIC.    


Se o reencaminhamento de consultas para Azure não conforme as suas necessidades, terá de fornecer o seu próprio solução DNS.  A solução de DNS precisa de:

-  Fornece resolução hostname adequado, por exemplo através do [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md).  Nota, se utilizar DDNS poderá ter de desativar a limpeza do registo de DNS, as concessões de DHCP do Azure muito longas e limpeza poderá remover prematuramente registos DNS. 
-  Fornece resolução recursiva adequado para permitir a resolução de nomes de domínio externo.
-  Ser acessível (TCP e UDP na porta 53) a partir dos clientes que serve e ser possível aceder à internet.
-  Protegida contra o acesso a partir da internet, a atenuar ameaças que pelos agentes externos.

> [AZURE.NOTE] Para um melhor desempenho, quando utiliza o Azure VMs como servidores DNS, devem ser desactivados IPv6 e um [Endereço IP público do nível da instância](../virtual-network/virtual-networks-instance-level-public-ip.md) devem ser atribuídas a cada servidor DNS VM.  

