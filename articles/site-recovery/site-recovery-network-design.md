<properties
    pageTitle="Estruturar a sua infraestrutura de rede para recuperação de falhas | Microsoft Azure"
    description="Este artigo aborda considerações de estrutura de rede para a recuperação de Site do Azure"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>Estruturar a sua infraestrutura de rede para recuperação de falhas

Este artigo é direcionado para profissionais de TI que se encontrem responsável pela criação, implementar e suporte continuidade do negócio e infraestrutura de recuperação (BCDR) falhas e que pretendem tirar partido do Microsoft Azure Site recuperação (recuperação automática) para suportar e melhorar o dos seus serviços BCDR. Este documento aborda práticas considerações para a implementação de servidor do Gestor de Máquina Virtual do Centro de sistema, as vantagens e desvantagens de sub-redes esticadas vs. sub-rede activação pós-falha e como estruturar recuperação de falhas para sites virtuais no Microsoft Azure.

## <a name="overview"></a>Descrição geral

[Azure Site recuperação (recuperação automática do sistema)](https://azure.microsoft.com/services/site-recovery/) é um serviço do Microsoft Azure que orquestra a proteção e recuperação das suas aplicações virtualizadas para fins de recuperação (BCDR) de falhas de continuidade de empresas. Este documento destina-se para o orientar o leitor durante o processo de estruturação de redes, focar criação intervalos IP e sub-redes no site de recuperação de falhas, quando replicação máquinas virtuais (VMs) utilizando a recuperação de sites.

Além disso, este artigo demonstra como recuperação de sites permite a criação e execução de um centro de dados virtual realizado para suportar os serviços BCDR ao tempo de teste ou falhas.

No mundo onde espera todos conectividade de 24/7, é mais importante do que nunca manter a sua infraestrutura e aplicações para cima e a ser executado. É o objetivo do continuidade do negócio e recuperação falhas (BCDR) restaurar os componentes falhadas, de modo a organização pode retomar rapidamente operações normais. Desenvolver estratégias de recuperação de falhas fazer em relação aos eventos pouco, devastador é muito um desafio. Este é devido a dificuldade em inerente as previsões no futuro, sobretudo, tal como está relacionada com a improváveis eventos e o custo de alto para fornecer as medidas adequadas de proteção contra catástrofes abrangente. 

Cruciais para BCDR planeamento, objectivo de tempo de recuperação (RTO) e recuperação ponto objectivo (RPO) devem ser definidos como parte de um plano de recuperação de falhas. Quando uma falhas atinja Centro de dados do cliente, utilizando a recuperação Site Azure, clientes podem rapidamente (RTO baixa) online trazer os respetivos máquinas virtuais replicadas localizadas no Centro de dados secundária ou Microsoft Azure com perda de dados mínimos (RPO baixa). 

Activação pós-falha é tornado possível por recuperação automática que inicialmente copia designadas máquinas virtuais a partir do Centro de dados principal para o Centro de dados secundária ou para Azure (consoante o cenário) e, em seguida, atualiza periodicamente as réplicas. Durante o planeamento de infraestrutura, design da rede considerado congestionamento potencial que pode impedir que da empresa de reunião RTO e objetivos RPO.  

Quando estiver a planear os administradores implementar uma solução de recuperação de falhas, uma das perguntas chaves perturbar é como a máquina virtual seria acessível depois de concluída a activação pós-falha. Recuperação automática permite ao administrador escolher a rede para que uma máquina virtual seria estar ligada após activação pós-falha. Se o site principal é gerido por um servidor VMM, em seguida, pode fazê-utilizar o mapeamento de rede. Consulte o artigo [preparar para mapeamento de rede](site-recovery-network-mapping.md) para obter mais detalhes.

Enquanto criava a rede para o site de recuperação, o administrador tem duas opções:

- Utilize um intervalo de endereço IP diferente para a rede no site de recuperação. Neste cenário, a máquina virtual após activação pós-falha irá obter um novo endereço IP e o administrador teria que efetuar uma atualização DNS. Leia mais sobre como fazer o DNS atualizar [aqui](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- Utilize o mesmo intervalo de endereços IP para a rede no site da recuperação. Em certos cenários administradores preferem manter os endereços IP que tenham no site principal, mesmo depois de ter o activação pós-falha. Num cenário de normal, um administrador teria que atualizar as rotas para indicar a nova localização dos endereços IP. Mas o cenário onde uma VLAN esticada é implementada entre a página principal e os sites de recuperação, manter os endereços IP para as máquinas virtuais torna-se uma opção apelativa. Manter o mesma IP endereços simplifica o processo de recuperação tirando ausente qualquer rede relacionadas com os passos de mensagem com falha.


Quando estiver a planear os administradores implementar uma solução de recuperação de falhas, uma das perguntas chaves em sua conta é como as aplicações serão alcançáveis depois de concluída a activação pós-falha. Aplicações modernas quase sempre são dependentes funcionamento em rede até certo ponto, por isso, com a mudança física que um serviço de um site para outro representa um desafio funcionamento em rede. Existem duas formas principais soluções de recuperação de falhas é tratado este problema. A primeira abordagem é manter os endereços IP fixos. Apesar dos serviços de mover e os servidores de alojamento a ser em diferentes localizações físicas, aplicações demorar a configuração de endereços IP com os mesmos para a nova localização. A segunda abordagem envolve completamente alterar o endereço IP durante a transição para o site recuperado. Cada abordagem tem várias variações de implementação que estão resumidas abaixo.

Enquanto criava a rede para o site de recuperação, o administrador tem duas opções:

## <a name="option-1-retain-ip-addresses"></a>Opção 1: Reter endereços IP 

A partir de uma perspetiva de processo de recuperação de falhas, utilizando o IP fixo endereços parece o método mais fácil para implementar, mas tiver um número de potenciais são os desafios mais que, na prática, torná-lo a abordagem menos popular. Recuperação de Site Azure fornece a capacidade para manter os endereços IP em todos os cenários. Antes de um decide reter IP, deve ser dada pensamento apropriado para os constrangimentos impõe as capacidades de activação pós-falha. Observe diga-nos fatores que podem ajudá-lo a tomar uma decisão para reter endereços IP, ou não. Isto pode ser excedido em duas formas, utilizando uma sub-rede esticada ou ao efetuar uma activação de sub-rede completo pós-falha.

### <a name="stretched-subnet"></a>Sub-rede alargado

Aqui sub-rede é disponibilizada em simultâneo no principal e localizações de DR. Em termos simples isto significa que pode mover um servidor e a respectiva configuração IP (camada 3) para o segundo site e a rede irá encaminhar o tráfego para a nova localização automaticamente. Este é comum para lidar com a partir de uma perspetiva de servidor mas tiver um número de desafios:

- A partir de uma perspetiva (camada de ligação de dados) camada 2, vai requerer equipamento de rede pode gerir uma VLAN esticada, mas este tornou menor, de um problema, tal como está agora amplamente disponível. Segundo e mais difícil é o problema que esticando VLAN domínio potenciais falhas é expandido para ambos os sites, essencialmente a tornar-se um ponto de falha único. Enquanto este é uma pouco ocorrência, tem aconteceu que uma difusão tempestade iniciado, mas não foi possível isolada. Estamos a ter encontrado mista opinião sobre este problema último e viu muitas implementações bem sucedidas, bem como "Pedimos irá nunca implementar esta tecnologia aqui".
- Sub-rede esticada não é possível se estiver a utilizar o Microsoft Azure como o site de DR.


### <a name="subnet-failover"></a>Sub-rede activação pós-falha

É possível implementar o sub-rede failover para obter as vantagens da solução sub-rede esticada descrito acima sem afastar sub-rede através de vários sites. Aqui qualquer determinada sub-rede seria presente no Site de 1 ou 2 do Site, mas nunca em ambos os locais em simultâneo. Para manter o espaço de endereços IP trabalho uma activação pós-falha, é possível ordenar através de programação para infraestrutura do router mover as sub-redes a partir de um site para outro. Num cenário de activação pós-falha que sub-redes teriam se mova juntamente com associado protegido VMs. A principal desvantagem desta abordagem é em caso de falha que tiver para mover a sub-rede toda, que pode ser OK, mas pode afectar as considerações de granularidade activação pós-falha. 

Vamos examinar como é possível criar uma réplica da suas VMs para uma localização de recuperação enquanto uma falha ao longo da sub-rede toda uma empresa fictícia com o nome Contoso. Em primeiro lugar vamos abordar como Contoso é administre respectivas sub-redes enquanto replicação VMs entre dois locais localizações e, em seguida, abordaremos como sub-rede activação pós-falha funciona quando [que Azure é utilizado como o site de recuperação de falhas](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Activação pós-falha a um site secundário no local

Diga-nos observe um cenário onde queremos reter IP de cada um das VMs e falhas com sobre a sub-rede concluída em conjunto. O site principal tem aplicações em execução no sub-rede 192.168.1.0/24. Quando a activação pós-falha acontece, todas as máquinas virtuais que fazem parte de nesta sub-rede vai ser Ocorreu uma falha ao longo para o site de recuperação e manter os respetivos endereços de IP. Rotas terá corretamente ser alterada para refletir o facto de que todas as máquinas virtuais que pertencem a sub-rede 192.168.1.0/24 agora ter passado para o site de recuperação. 

Na ilustração seguinte as rotas entre o site principal e site recuperação, site terceiro e site principal e site terceiro e site recuperação terá corretamente ser alterada. 

As seguintes imagens mostra as sub-redes antes da activação pós-falha. Sub-rede 192.168.0.1/24 está ativa no Site principal antes da activação pós-falha e fica ativo do Site recuperação após a activação pós-falha 

![Antes de activação pós-falha](./media/site-recovery-network-design/network-design2.png)

Antes de activação pós-falha


A imagem abaixo mostra as redes e sub-redes após activação pós-falha.
    
![Depois de activação pós-falha](./media/site-recovery-network-design/network-design3.png)

Depois de activação pós-falha

No seu site secundário é no local e estiver a utilizar um servidor VMM para geri-lo, em seguida, quando activar a proteção por para uma máquina virtual específica, a recuperação automática atribuirá à rede recursos de acordo com o fluxo de trabalho seguinte:

- Recuperação automática atribui um endereço IP para cada interface de rede na máquina virtual do conjunto de endereços IP estático definido da rede relevantes para cada instância do sistema Center VMM.
- Se o administrador define o mesmo conjunto de endereços IP para a rede no site de recuperação como que o conjunto de endereços IP da rede no site principal, enquanto o endereço IP à máquina de virtual réplica recuperação automática a atribuir seria atribuir o mesmo endereço IP que da máquina virtual principal.  O período de inquérito é reservado no VMM mas não definido como IP activação pós-falha. IP de activação pós-falha está definido imediatamente antes de activação pós-falha.

![Manter o endereço IP](./media/site-recovery-network-design/network-design4.png)
    
Figura 5

Figura 5 mostra as definições de activação pós-falha em TCP/IP para a máquina de virtual réplica (na consola do Hyper-V). Estas definições seriam ser preenchidas antes da máquina virtual é iniciada após uma activação pós-falha

Se o mesmo IP não estiver disponível, recuperação automática seria alocar algumas outras endereço IP disponível a partir do conjunto de endereços IP definido. 

Depois da VM estiver ativada para protecção pode utilizar das seguintes scripts de exemplo para verificar o IP que lhe tenha sido atribuído a máquina virtual. O mesmo período de inquérito seria definir como activação pós-falha IP e atribuído a VM no momento da activação pós-falha:

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] O cenário onde máquinas virtuais utilizar DHCP, a gestão de endereços IP é completamente fora do controlo de recuperação automática. Um administrador tem de se certificar de que o servidor DHCP que servem os endereços IP no site de recuperação pode servir do mesmo intervalo de que o site principal.

#### <a name="failover-to-azure"></a>Activação pós-falha ao Azure

Azure Site recuperação (recuperação automática do sistema) permite Microsoft Azure ser utilizado como um site de recuperação de falhas para máquinas virtuais. Neste caso, terá de lidar com um constrangimento mais. 

Vamos analisar um cenário onde numa empresa fictícia denominada Banco Woodgrove tem infraestrutura no local que aloja a sua linha de aplicações empresariais e estiver a alojar as suas aplicações móveis Azure. Conectividade entre Woodgrove banco VMs nos servidores do Azure e no local é fornecida por um site para site (S2S) rede privada Virtual (VPN). S2S VPN permite rede virtual do Banco Woodgrove no Azure para ser visualizadas como uma extensão de rede no local de Woodgrove bancário. Esta comunicação é activada por S2S VPN entre o limite de Woodgrove banco e Azure rede virtual. Agora Woodgrove pretende utilize a recuperação automática para criar uma réplica da suas das cargas de trabalho em execução no seu centro de dados para Azure. Esta opção às necessidades dos Woodgrove, o qual pretende uma opção de DR económica e consiga armazenar dados nos ambientes de nuvem público. Woodgrove tem lidar com as aplicações e configurações que dependem codificada endereços IP, por conseguinte que tenham um requisito para reter endereços IP para as suas aplicações após uma falha ao longo para Azure.

Woodgrove decidiu atribuir endereços IP do intervalo de endereços IP (172.16.1.0/24, 172.16.2.0/24) aos seus recursos em execução no Azure.

Para Woodgrove possam criar uma réplica da suas máquinas virtuais para Azure enquanto mantém os endereços IP, uma rede Virtual Azure tem de ser criado. Deverá ser uma extensão da rede no local para que as aplicações podem activação em pós-falha a partir do site no local para Azure de forma totalmente integrada. Azure permite-lhe adicionar site para o site, bem como ponto-para-site conectividade de VPN para as redes virtuais criadas no Azure. Quando configurar a sua ligação de site para o site, o Azure rede permite-lhe encaminhar o tráfego para a localização no local (Azure telefona-rede local) apenas se o intervalo de endereços IP é a diferença entre o intervalo de endereços IP no local, porque o Azure não suporta sub-redes esticar.  Isto significa que se tiver um 192.168.1.0/24 sub-rede no local, não pode adicionar uma rede local 192.168.1.0/24 na rede Azure. Isto é esperado porque Azure não sabe que não existem sem VMs ativas na sub-rede e que está a ser criada a sub-rede apenas para fins de DR. Possam corretamente encaminhar o tráfego de rede terminar uma rede Azure sub-redes na rede e da rede local tem não entram em conflito. 

![Antes de sub-rede activação pós-falha](./media/site-recovery-network-design/network-design7.png)

Antes de activação pós-falha

Para ajudar a Woodgrove satisfazer as suas necessidades de negócio, precisamos de implementar os fluxos de trabalho seguintes:

- Criar uma rede adicional, diga-nos chame-recuperação de rede, onde deverá ser criadas máquinas virtuais com sobre.
- Para se certificar de que o PI para um VM é mantido após uma activação pós-falha, aceda ao separador Configurar em Propriedades do VM, especifique o período de inquérito mesmo que a VM tem no local e, em seguida, clique em Guardar. Quando a VM é Ocorreu uma falha ao longo do, o Azure Site recuperação atribuir o PI fornecido à máquina virtual. 

![Propriedades de rede](./media/site-recovery-network-design/network-design8.png)

Quando a activação pós-falha é acionada e as máquinas virtuais são criadas na rede recuperação com o PI pretendido, conectividade para esta rede é possível estabelecer uma [Vnet Vnet ligação](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Se necessário, pode ser script esta ação.  Como debatido na secção anterior sobre sub-rede falha na ligação, mesmo no caso de activação pós-falha ao Azure, rotas teria corretamente ser alterada para refletir que esse 192.168.1.0/24 agora foi movido para Azure. 

![Depois de sub-rede activação pós-falha](./media/site-recovery-network-design/network-design9.png)

Depois de activação pós-falha

Se não tiver uma rede' Azure' conforme mostrado na imagem acima. Pode criar uma ligação vpn do site para site entre o seu 'Site principal' e 'Recuperação de rede' após a activação pós-falha.  


## <a name="option-2-changing-ip-addresses"></a>Opção 2: Alterar endereços IP

Esta abordagem parece ser mais prevalecem com base no que vamos vistos. Bastam sob a forma de alterar o endereço IP do cada VM que é, normalmente o activação pós-falha. Um sistema de devolução desta abordagem requer a rede para 'saber' que a aplicação que foi na IPx está agora na IPy recebida. Mesmo que existam IPx e IPy nomes lógicos, DNS entries, normalmente, tem de ser alteradas ou esvaziada em toda a rede e entradas em cache em tabelas de rede tem de ser actualizado ou esvaziada, por conseguinte, um tempo de inatividade pode ser visto dependendo de como a infraestrutura de DNS foi configuração. Estes problemas podem ser atenuados ao utilizar os valores TTL baixos no caso de aplicações de intranet e utilizar o [Gestor de tráfego Azure com recuperação automática](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) para aplicações baseados na internet

### <a name="changing-the-ip-addresses---illustration"></a>Alterar os endereços IP - ilustração

Diga-nos observe o cenário onde está a planear utilizar diferentes IPs em toda a página principal e os sites de recuperação. No seguinte exemplo Estamos também têm um terceiro do site a partir de onde as aplicações alojado no monitor principal ou recuperação site pode ser acedido.

![IP diferentes - antes de activação pós-falha](./media/site-recovery-network-design/network-design10.png)

Figura 11

Figura 11 existem algumas aplicações alojadas no sub-rede 192.168.1.0/24 de sub-rede no site principal e tenham sido configurados para obter no site de recuperação sub-rede 172.16.1.0/24 após uma activação pós-falha. VPN ligações/rede rotas tem sido configuradas corretamente para que todos os sites de três possam aceder-se entre si.
 
Como figura 12 mostra, após uma falha ao longo de uma ou mais aplicações, serão restaurados na sub-rede de recuperação. Neste caso podemos não são limitados a activação pós-falha a sub-rede inteira ao mesmo tempo. Não são necessárias alterações para reconfigurar rotas VPN ou de rede. Uma activação e algumas atualizações de DNS irão Certifique-se de que as aplicações continuam a acessíveis. Se o DNS estiver configurado para permitir actualizações dinâmicas as máquinas virtuais seria registar próprios utilizando o novo IP assim que começam após uma activação pós-falha. 

![IP diferentes - após activação pós-falha](./media/site-recovery-network-design/network-design11.png)

Figura 12

Após uma falha com sobre máquina réplica virtual poderá ter um endereço IP que não seja o mesmo que o endereço IP da máquina virtual principal. Máquinas virtuais irá actualizar o servidor DNS que estiverem a utilizar após o início. As entradas de DNS, normalmente, tem de ser alteradas ou esvaziada em toda a rede e entradas em cache em tabelas de rede têm de ser actualizado ou esvaziada, pelo que não é invulgar são confrontadas com indisponibilidade enquanto executada estas alterações de estado. Este problema pode ser atenuado ao:

- Utilizar os valores TTL baixos para aplicações intranet.
- Utilizar o Gestor de tráfego Azure com recuperação automática para internet aplicações baseadas em.
- Utilizando o seguinte script do seu plano de recuperação para actualizar o servidor de DNS para se certificar de uma atualização atempada (o script não é necessário se o registo DNS dinâmico está configurado)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>Alterar os endereços IP – DR para Azure

A mensagem de blogue de [Configuração da infraestrutura de rede para o Microsoft Azure como um Site de recuperação de falhas](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explica como configurar a infraestrutura de rede Azure necessária quando reter endereços IP não é um requisito. -Começa com que descrevem a aplicação e, em seguida, procure na como configurar o funcionamento em rede no local e no Azure e, em seguida, concluindo com como o fazer uma activação de teste e uma activação planeada pós-falha.



## <a name="next-steps"></a>Próximos passos

[Saiba](site-recovery-network-mapping.md) como recuperação de sites mapas de origem e destino redes quando um servidor de VMM está a ser utilizado para gerir o site principal. 
