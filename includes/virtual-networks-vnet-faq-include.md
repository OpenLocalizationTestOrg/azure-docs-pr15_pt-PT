## <a name="virtual-network-basics"></a>Noções básicas de rede virtual

### <a name="what-is-an-azure-virtual-network-vnet"></a>O que é uma rede Azure Virtual (VNet)?

Pode utilizar VNets aprovisionar e gerir redes privadas virtuais (VPNs) no Azure e, opcionalmente, ligar a VNets com outros VNets no Azure ou com o seu no local infraestrutura de TI para criar soluções híbrido ou a publicação em local. Cada VNet que criar tem o seu próprio bloco CIDR e pode ser ligada a outras redes VNets e no local, desde que os blocos CIDR não entrarem em conflito. Também tem controlos das definições do servidor DNS para VNets e segmentação do VNet em sub-redes.

Utilize VNets para:

- Criar uma dedicada apenas na nuvem virtual rede privada

    Por vezes, não requerem uma configuração de publicação em local para a sua solução. Quando cria uma VNet, seus serviços e VMs dentro da sua VNet podem comunicar diretamente e segura com os outros na nuvem. Mantém o tráfego de VNet de forma segura, mas ainda permite-lhe configurar ligações de ponto final para os serviços que necessitam de comunicação para a Internet como parte da sua solução e VMs.

- Em segurança alargar o seu centro de dados

    Com VNets, pode criar tradicionais VPNs de (S2S) de site para o site para segura dimensionar a capacidade do seu centro de dados. S2S VPNs utilize IPSEC para fornecer uma ligação segura entre o seu gateway VPN da empresa e Azure.

- Ativar cenários de nuvem híbrido

    VNets dar-lhe a flexibilidade para suportar uma variedade de cenários de nuvem híbrido. Em segurança pode ligar a aplicações baseadas na nuvem para qualquer tipo de sistema no local, tal como mainframes e sistemas de Unix.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>Como sei se precisar de uma rede virtual?

Visite a [Descrição geral da rede Virtual](../articles/virtual-network/virtual-networks-overview.md) para ver uma tabela de decisão que irá ajudá-lo a decidir a melhor opção de estrutura de rede por si.

### <a name="how-do-i-get-started"></a>Como posso começar a?

Visite [a documentação da rede Virtual](https://azure.microsoft.com/documentation/services/virtual-network/) para começar a utilizar. Esta página tem ligações comuns passos de configuração, bem como informações que irão ajudá-lo a compreender os aspetos que terá de ter em consideração ao estruturar a rede virtual.

### <a name="what-services-can-i-use-with-vnets"></a>Que serviços pode utilizar com VNets?

VNets podem ser utilizados com uma variedade de diferentes serviços do Azure, como o serviços em nuvem (PaaS), máquinas virtuais e Web Apps. No entanto, existem alguns serviços que não são suportados num VNet. Verifique o serviço específico que pretende utilizar e certifique-se de que é compatível.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Pode utilizar VNets sem conectividade de publicação em local?

Sim. Pode utilizar uma VNet sem utilizar conectividade de site para o site. Isto é particularmente útil se pretender executar controladores de domínio e em farms do SharePoint no Azure.

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Que ferramentas utilizar para criar um VNet?

Pode utilizar as seguintes ferramentas para criar ou configurar uma rede virtual:

- Azure Portal (para classic e VNets do Gestor de recursos).

- Um ficheiro de configuração de rede (netcfg - para apenas VNets clássico). Consulte o artigo [Configurar uma rede virtual utilizando um ficheiro de configuração de rede](../articles/virtual-network/virtual-networks-using-network-configuration-file.md).

- PowerShell (para classic e VNets do Gestor de recursos).

- Clip Azure (para classic e VNets do Gestor de recursos).

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Intervalos de endereços que pode utilizar no meu VNets?

Pode utilizar intervalos de endereços IP públicos e qualquer intervalo de endereços IP foi definido no [RFC 1918](http://tools.ietf.org/html/rfc1918).

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Pode ter endereços IP públicos no meu VNets?

Sim. Para mais informações sobre intervalos de endereços IP públicos, consulte o artigo [espaço de endereços IP público numa rede Virtual (VNet)](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md). Tenha em atenção que o público IPs não será diretamente acessíveis a partir da Internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>Existe um limite para o número de sub-redes na minha rede virtual?

Não existe nenhum limite o número de sub-redes que utilizar dentro de uma VNet. Todas as sub-redes tem de estar totalmente contidas no espaço de endereços de rede virtual e não se devem sobrepor com um do outro.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Existem restrições sobre como utilizar endereços IP dentro estas sub-redes?

Azure reserva-se alguns endereços IP dentro de cada sub-rede. Os endereços IP primeiros e últimos das sub-redes são reservados para conformidade do protocolo, juntamente com 3 mais endereços utilizada para serviços Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Como pequenos e grandes como podem ser VNets e sub-redes?

O menor que suportamos é uma /29 e o maior é um /8 (utilizando as definições de sub-rede CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Posso trazer meu VLANs para Azure utilizando VNets?

Não. VNets são as Sobreposições de Layer 3. Azure não suporta qualquer semântica de camada-2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Pode especificar políticas de encaminhamento personalizadas no meu VNets e sub-redes?

Sim. Pode utilizar o utilizador definidos encaminhamento (UDR). Para mais informações sobre UDR, visite [rotas de definidos pelo utilizador e reencaminhamento IP](../articles/virtual-network/virtual-networks-udr-overview.md).

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNets suportam multicast ou de difusão?

Não. Não suportamos multicast ou de difusão.

### <a name="what-protocols-can-i-use-within-vnets"></a>Quais os protocolos pode utilizar dentro VNets?

Pode utilizar protocolos padrão com base em IP dentro VNets. No entanto, multicast, difusão, IP no IP pacotes encapsulated e pacotes de encaminhamento GRE (encapsulamento genérico) são bloqueados dentro VNets. Protocolos padrão que funcionam incluem:

- TCP
- UDP RECOMENDADAS
- ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Posso ping a minha routers predefinido dentro de uma VNet?

Não.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Pode utilizar tracert para diagnosticar conectividade?

Não.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Posso adicionar sub-redes depois do VNet é criado?

Sim. Sub-redes podem ser adicionadas a VNets em qualquer altura desde que o endereço de sub-rede não fizer parte de outra sub-rede a VNet.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Posso modificar o tamanho da minha sub-rede após posso criá-lo?

Pode adicionar, remover, expandir ou reduzir uma sub-rede se não existem VMs ou serviços implementados dentro da mesma, utilizando os cmdlets do PowerShell ou o ficheiro NETCFG. Também pode adicionar, remover, expandir ou encolher qualquer prefixos desde que as sub-redes que contêm VMs ou serviços não são afetadas pela alteração.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Posso modificar sub-redes depois que criei-los?

Sim. Pode adicionar, remover e modificar os blocos CIDR utilizados por um VNet.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>Posso ligar à internet se estou a utilizar os meus serviços num VNet?

Sim. Todos os serviços implementados dentro de uma VNet podem ligar-se à internet. Cada serviço em nuvem implementado no Azure tem um VIP publicamente endereçável atribuído à mesma. Terá de definir os pontos finais de entrada para PaaS funções e os pontos finais para máquinas virtuais activar estes serviços aceitar ligações a partir da internet.

### <a name="do-vnets-support-ipv6"></a>VNets suporta o IPv6?

Não. Não pode utilizar o IPv6 com VNets neste momento.

### <a name="can-a-vnet-span-regions"></a>Pode um VNet abranger regiões?

Não. Um VNet está limitado a uma única região.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Pode ligar um VNet para outra VNet no Azure?

Sim. Pode criar VNet para comunicação VNet utilizando REST APIs ou o Windows PowerShell. Também pode ligar VNets através do VNet efectuado. Ver mais detalhes sobre efectuado [aqui.](../articles/virtual-network/virtual-network-peering-overview.md)

## <a name="name-resolution-dns"></a>Resolução do nome de (domínio DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Quais são as opções de DNS para VNets?

Utilize a tabela de decisão na página de [Resolução de nomes para VMs e função instâncias](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para guiá-lo através de todas as opções de DNS disponíveis.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Pode especificar os servidores DNS para um VNet?

Sim. Pode especificar endereços IP do servidor DNS nas definições de VNet. Será aplicada como o servidor (es) DNS predefinida para todos os VMs na VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Quantos servidores DNS que pode a especificar

Pode especificar até 12 servidores DNS.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Posso modificar os meus servidores DNS depois de ter criei da rede?

Sim. Pode alterar a lista de servidor de DNS para o seu VNet em qualquer altura. Se alterar a sua lista de servidor de DNS, terá de reiniciar cada um das VMs no seu VNet digitadas para continuar o novo servidor DNS.


### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>O que é o DNS Azure fornecido pela e funciona VNets?

Azure fornecido pela DNS é um serviço DNS do inquilino com várias oferecido pela Microsoft. Azure regista todas as suas VMs e instâncias de funções neste serviço. Este serviço fornece resolução do nome ao nome do anfitrião para VMs e instâncias de função contidas no mesmo serviço em nuvem e ao FQDN para VMs e instâncias de funções no VNet mesmo.

> [AZURE.NOTE] Existe uma limitação neste momento para os primeira serviços em 100 nuvem na rede virtual para resolução de nomes de inquilino cruzada através de DNS Azure fornecido pela. Se estiver a utilizar o seu servidor de DNS, esta limitação não se aplica.

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>Pode substituir as minhas definições de DNS numa por-VM / base do serviço?

Sim. Pode definir os servidores DNS numa base de serviço por nuvem para substituir as predefinições de rede. No entanto, recomendamos que utilize toda a rede DNS quanto possível.

### <a name="can-i-bring-my-own-dns-suffix"></a>Posso trazer o meu próprio sufixo DNS?

Não. Não é possível especificar um sufixo DNS personalizado para o seu VNets.

## <a name="vnets-and-vms"></a>VNets e VMs

### <a name="can-i-deploy-vms-to-a-vnet"></a>Pode implementar VMs para um VNet?

Sim.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>Pode implementar Linux VMs para um VNet?

Sim. Pode implementar qualquer distro do Linux suportado pelo Azure.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>O que é a diferença entre um VIP público e um endereço IP interno?

- Um endereço IP interno é um endereço IP que está atribuído a cada VM dentro de uma VNet pelo DHCP. Não é direcionado para o público público. Se tiver criado uma VNet, o endereço IP interno é atribuído a partir do intervalo que especificado nas definições sub-rede do seu VNet. Se não tiver um VNet, um endereço IP interno ainda será atribuído. O endereço IP interno irá permanecer com a VM para a sua vida útil, a menos que VM é desatribuído.

- Um VIP público é o endereço IP público que está atribuído a sua Balanceador de carga ou serviço na nuvem. Não está atribuído diretamente para o seu NIC VM. O VIP fique com o serviço de nuvem que é atribuída à até todas as VMs nesse serviço na nuvem são desatribuído ou eliminadas. Nesse momento, é disponibilizada.

### <a name="what-ip-address-will-my-vm-receive"></a>Qual é o endereço IP meu VM vai receber?

- **Endereço IP interno-** Se implementar uma VM para um VNet, a VM recebe um endereço IP interno a partir de um conjunto de endereços IP internos que especificar. Comunicam VMs dentro de VNets utilizando endereços IP internos. Apesar de Azure atribui um endereço IP interno dinâmico, pode pedir um endereço estático para sua VM. Para saber mais sobre os endereços IP internos estáticos, visite [como configurar um endereço IP estático interno](../articles/virtual-network/virtual-networks-reserved-private-ip.md).

- **VIP-** A VM também está associada um VIP, apesar de um VIP nunca será atribuído a VM diretamente. Um VIP é um endereço IP público que pode ser atribuído no seu serviço de nuvem. Pode, opcionalmente, reserve uma VIP do seu serviço de nuvem.

- **ILPIP-** Também pode configurar um endereço IP público nível da instância (ILPIP). ILPIPs diretamente estão associadas a VM, em vez do serviço de nuvem. Para saber mais sobre ILPIPs, visite o [Nível da instância descrição geral de IP público](../articles/virtual-network/virtual-networks-instance-level-public-ip.md).

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Pode reservar um endereço IP interno para uma VM que crio tarde?

Não. Não é possível reservar um endereço IP interno. Se houver um endereço IP interno serão atribuída a uma instância da função ou VM pelo servidor DHCP. Esse VM pode ou não ser aquele que pretende que o endereço IP interno a serem atribuídas à. No entanto, pode, alterar o endereço IP interno de uma VM já criado para qualquer endereço IP interno disponível.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>Efetue a alteração de endereços IP interna para VMs num VNet?

Sim. Endereços IP internos permanecem com a VM durante a sua vida útil, a menos que a VM é desatribuída. Quando uma VM é desatribuída, o endereço IP interno é editado, a menos que definiu um endereço IP estático interno para sua VM. Se a VM é simplesmente parada (e não colocar o estado **parado (Deallocated)**) o endereço IP permanecerá atribuído para a VM.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>Manualmente posso atribuir endereços IP para NIC no VMs?

Não. Não tem de alterar as propriedades de interface de VMs. Quaisquer alterações podem conduzir a perder potencialmente conectividade para a VM.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>O que acontece aos meus endereços IP se posso encerrar uma VM?

Nada. Os endereços IP (VIP público e endereço IP interno) irão permanecer com o seu serviço na nuvem ou VM.

> [AZURE.NOTE] Se pretender simplesmente encerrar a VM, não utilize o Portal de gestão para fazê-lo. Atualmente, o botão encerramento irá retirar a máquina virtual.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>Posso mover VMs a partir de uma sub-rede para outra sub-rede num VNet sem novamente implementar?

Sim. Pode encontrar mais informações [aqui](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md).

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Pode configurar um endereço de MAC estático para minha VM?

Não. Um endereço MAC não pode ser configurado de forma estática.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>O endereço MAC permanecerá igual para minha VM depois de ter sido criado?

Sim, o endereço MAC permanecerá igual para uma VM apesar da VM foi parada (deallocated) e novamente.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Posso ligar à internet a partir de uma VM num VNet?

Sim. Todos os serviços implementados dentro de uma VNet podem ligar-se à Internet. Para além disso, cada serviço em nuvem implementado no Azure tem um VIP publicamente endereçável atribuído à mesma. Tem de definir os pontos finais de entrada para PaaS funções e os pontos finais para VMs activar estes serviços aceitar ligações a partir da Internet.

## <a name="vnets-and-services"></a>VNets e serviços

### <a name="what-services-can-i-use-with-vnets"></a>Que serviços pode utilizar com VNets?

Só pode utilizar os serviços de cluster dentro VNets. Serviços de cluster estão limitados aos serviços em nuvem (funções web e trabalhador) e VMs.

### <a name="can-i-use-web-apps-with-virtual-network"></a>Pode utilizar aplicações Web com a rede Virtual?

Sim. Pode implementar Web Apps no interior de um VNet com o Auxiliar (ambiente de serviço de aplicação). Adicionar a que, Web Apps pode segura ligar e aceder aos recursos no seu VNet Azure, caso tenha configurado para o seu VNet ponto-site. Para mais informações, consulte o seguinte:


- [Criar aplicações Web num ambiente de aplicação de serviço](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)

- [Integração de rede Virtual de aplicações Web](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)

- [Utilizar VNet integração e ligações de híbrido Web Apps](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)

- [Integrar uma aplicação web uma rede Virtual do Azure](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Pode implementar serviços em nuvem com funções web e trabalhador (PaaS) num VNet?

Sim. Pode implementar os serviços de PaaS dentro VNets.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>Como posso implementar o PaaS funções para um VNet?

Consegui-lo ao especificar o nome de VNet e os mapeamentos de /subnet função na secção de configuração de rede da sua configuração de serviço. Não necessita de atualizar qualquer um dos seus binários.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Pode mover os meus serviços e terminar VNets?

Não. Não pode mover serviços e terminar VNets. Tem de eliminar e implementar o serviço para movê-lo para outro VNet novamente.

## <a name="vnets-and-security"></a>VNets e segurança

### <a name="what-is-the-security-model-for-vnets"></a>O que é o modelo de segurança para VNets?

VNets são completamente isoladas do entre si e outros serviços hospedados no infraestrutura do Azure. Um VNet é um limite de fidedignidade.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>Posso definir o ACL ou NSGs no meu VNets?

Não. Não pode associar as ACL ou NSGs para VNets. No entanto, ACL podem ser definidas nos pontos finais de entrada para VMs que foram implementados para um VNets e NSGs podem ser associados a sub-redes ou NIC.

### <a name="is-there-a-vnet-security-whitepaper"></a>Existe um documento técnico de segurança de VNet?

Sim. Pode transferi-lo [aqui](http://go.microsoft.com/fwlink/?LinkId=386611).

## <a name="apis-schemas-and-tools"></a>APIs, esquemas e ferramentas

### <a name="can-i-manage-vnets-from-code"></a>Pode gerir VNets a partir do código?

Sim. Pode utilizar REST APIs para gerir conetividade a VNets e cruzada local. Mais informações podem ser encontradas [aqui](http://go.microsoft.com/fwlink/?LinkId=296833).

### <a name="is-there-tooling-support-for-vnets"></a>Existe suporte de ferramentas para VNets?

Sim. Pode utilizar as ferramentas de PowerShell e linha de comandos para uma variedade de plataformas. Mais informações podem ser encontradas [aqui](http://go.microsoft.com/fwlink/?LinkId=317721).
