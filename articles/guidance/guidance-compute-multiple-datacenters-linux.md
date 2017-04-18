<properties
   pageTitle="A ser executada Linux VMs em várias regiões para elevada disponibilidade | Arquitetura de referência | Microsoft Azure"
   description="Como implementar VMs em várias regiões Azure para elevada disponibilidade e RDP."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/21/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-in-multiple-regions-for-high-availability"></a>A ser executada Linux VMs em várias regiões para elevada disponibilidade

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [A ser executada Linux VMs em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters-linux.md)
- [A executar o Windows VMs em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters.md)

Neste artigo, recomendamos que um conjunto de práticas para executar máquinas virtuais do Linux (VMs) nas regiões Azure múltiplos, para alcançar a disponibilidade e uma infraestrutura de recuperação de falhas robusta.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource groups] e clássico. Este artigo utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Uma arquitetura de região com várias pode fornecer maior disponibilidade que implementar a uma única região. Se uma falha de regional afetar a região principal, pode utilizar o [Gestor de tráfego] [ traffic-manager] Falha ao longo à região de secundário. Também pode ajudar a esta arquitetura se falhar um subsystem individual da aplicação.

Existem várias abordagens gerais para alcançar elevada disponibilidade nos centros de dados:   
  
- Activo/passivo com quente em espera. Vai tráfego para uma região, enquanto o aguarda até em modo de espera. VMs na região secundário são atribuídos e a execução sempre.

- Activo/passivo com suspensão fria. O mesmo, mas VMs na região secundário não são atribuídos até necessário para activação pós-falha. Esta abordagem custos menor a executar, mas geralmente terão de já vez durante uma falha.

- Activo/activo. Ambas as regiões estão ativas e os pedidos de são distribuído entre elas. Se um centro de dados ficar disponível, é assumido terminar rotação.

Esta arquitetura foca-se em activo/passivo com suspensão quente, utilizando o Gestor de tráfego para activação pós-falha. Tenha em atenção que poderia implementar um pequeno número de VMs para suspensão quente e, em seguida, dimensionar saída, conforme necessário.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte cria na arquitetura apresentada na [fiabilidade de adicionar a uma arquitetura de camadas no Azure](guidance-compute-n-tier-vm-linux.md). 

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está ativada "cluster - página de região (Linux) multi.

![[0]][0]

- **Regiões principais e secundários**. Esta arquitetura utiliza duas regiões para alcançar maior disponibilidade. Uma é a região principal. Durante as operações normais, o tráfego de rede é encaminhado para a região principal. Mas se que ficar disponível, o tráfego é encaminhado para a região secundária.

- ** [Azure tráfego Gestor] [ traffic-manager] ** encaminha pedidos recebidos à região de principal. Se esse região ficar disponível, o Gestor de tráfego Falha ao longo à região de secundário. Para mais informações, consulte a secção [Configurar o Gestor de tráfego](#configuring-traffic-manager).

- **Grupos de recursos**. Crie separados [grupos de recursos] [ resource groups] para a região primária, secundário região e para o Gestor de tráfego. Isto dá flexibilidade para gerir o cada região como um único conjunto de recursos. Por exemplo, pode implementar uma região, sem ser para baixo da outra. [Ligar os grupos de recursos][resource-group-links], para que pode executar uma consulta para listar todos os recursos para a aplicação.

- **VNets**. Crie um VNet separada para cada região. Certifique-se de que não se sobrepõem os espaços de endereço.

- **Apache Cassandra** implementada no dados centros de entre as regiões Azure. Centros de dados Cassandra são implementados em diferentes regiões Azure para elevada disponibilidade. Dentro de cada uma das regiões, nós são configuradas no modo deverá ter em consideração bastidor com falhas e domínios, para RDP no interior da área de atualização.

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar o seu próprio arquitetura de referência seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="regional-pairing"></a>Emparelhar regionais

Cada região Azure é emparelhado com outra região da geografia mesmo. Em geral, selecione regiões mesmo par regional (por exemplo, Leste dos EUA 2 e Central-nos). Vantagens de fazê-lo incluem:

- Se existir uma falha de abrangente, recuperação de pelo menos uma região terminar cada par tem prioridade.

- Atualizações de sistema Azure planeadas são lançadas regiões emparelhadas sequencialmente, para minimizar o tempo de inatividade possível.

- Pares residem na geografia mesmo, para cumprir os requisitos de residência de dados.

No entanto, certifique-se de que ambas as regiões suportam todos os serviços Azure necessários para a sua aplicação (consulte a [secção serviços por região][services-by-region]). Para mais informações sobre pares regionais, consulte o artigo [empresas continuidade e falhas recuperação (BCDR): Azure emparelhadas regiões][regional-pairs].

### <a name="traffic-manager-configuration"></a>Configuração do Gestor de tráfego

Tenha em consideração os seguintes pontos ao configurar o tráfego gestor para o seu cenário:

- **Encaminhamento.** O Gestor de tráfego suporta vários [algoritmos de encaminhamento][tm-routing]. Para o cenário descrito neste artigo, utilize o encaminhamento de _prioridade_ (anteriormente chamado _activação pós-falha_ encaminhamento). Com esta definição, o Gestor de tráfego envia todos os pedidos de à região de principal, a menos que a região primária torna-se inacessível. Nesse momento,-lo automaticamente Falha ao longo à região de secundário. Consulte o artigo [Configurar activação pós-falha método de encaminhamento][tm-configure-failover].

- **Sonda de estado de funcionamento.** Gestor de tráfego utiliza uma HTTP (ou HTTPS) [sonda] [ tm-monitoring] para monitorizar a disponibilidade de cada região. A sonda verifica se ocorre uma resposta de HTTP 200 para um caminho de URL especificado. Como prática recomendada, crie um ponto final que reporta o estado de funcionamento geral da aplicação e utilizar este ponto final para a sonda de estado de funcionamento. Caso contrário, a sonda pode comunicar um ponto final de "Saudável" quando críticas peças da aplicação são realmente a falhar. Para obter mais informações, consulte o artigo [Padrão de monitorização do Estado de funcionamento do ponto final][health-endpoint-monitoring-pattern].

Quando o Gestor de tráfego Falha ao longo do, existe um período de tempo quando os clientes não é possível alcançar a aplicação, que pode ser alguns minutos. Dois factores afetam a duração total:

- A sonda de estado de funcionamento tem de detectar o Centro de dados principal tornou inacessível.

- Os servidores DNS tem de atualizar os registos DNS em cache para o endereço IP, que depende de DNS time to live (TTL). O TTL predefinido é de 300 segundos (5 minutos), mas pode configurar este valor ao criar o perfil do Gestor de tráfego.

Para obter detalhes, consulte o artigo [Sobre como Gestor de tráfego de monitorização][tm-monitoring].

Se o Gestor de tráfego falhar ao longo do, recomendamos que efetuar uma reposição de recurso manual, em vez de automaticamente a falhar novamente. Certifique-se de que todos os subsistemas de aplicação estão saudáveis pela primeira vez. Caso contrário, pode criar uma situação onde a aplicação e para trás inverte entre centros de dados.

Por predefinição, o tráfego Gestor automaticamente falha novamente. Para evitar esta situação, diminua manualmente a prioridade da região primária depois de um evento de activação pós-falha. Por exemplo, suponha que a região primária é a prioridade 1 e secundária é a prioridade 2. Depois de uma falha na ligação, defina a região principal para prioridade 3, para evitar a reposição de recurso automática. Quando estiver pronto para voltar atrás, atualize a prioridade para 1.

O seguinte comando Azure clip atualiza a prioridade:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --priority 3
```    

Outra forma de evitar Flip-flop é desligar temporariamente o ponto final:

```bat
azure network traffic-manager  endpoint set --resource-group <resource-group> --profile-name <profile>
    --name <traffic-manager-name> --type AzureEndpoints --status Disabled
```    

Consoante a causa de uma activação pós-falha, poderá ter de redploy os recursos dentro de uma região. Antes de voltar a falhar, execute um teste de disponibilidade operacional. O teste deve verificar coisas como:

- VMs estão configurados corretamente. (Todo o software necessário estiver instalado, o IIS está em execução, etc.)

- Subsistemas da aplicação estão em bom Estados.

- Testar funcional. (Por exemplo, a camada de base de dados é acessível a partir da camada web.)

### <a name="cassandra-deployment-across-multiple-regions"></a>Implementação de Cassandra através de vários regiões

Centros de dados Cassandra são divisões das cargas de trabalho: um grupo de nós relacionadas que estão configurados em conjunto dentro de um cluster de separação de replicação e a carga de trabalho.

Recomendamos que [DataStax Enterprise] [ datastax] para produção utilizar. Para obter mais informações sobre como executar DataStax no Azure, consulte o artigo [Guia de implementação de empresa DataStax para Azure][cassandra-in-azure]. As seguintes recomendações gerais aplicam-se para qualquer edição Cassandra.

- Atribua um endereço IP público para cada nó. Permite que os clusters comunicar através de regiões utilizando a infraestrutura de estrutura Azure, fornecendo débito alto, baixa custo.

- Seguro nós utilizando as configurações de NSG, permitindo que o tráfego de anfitriões conhecidos, incluindo os clientes e outros nós de cluster e apenas para e de firewall adequado. Tenha em atenção que Cassandra utiliza portas diferentes para comunicação, OpsCenter, motores e assim sucessivamente. Para utilização de porta no Cassandra, consulte o artigo [Configurar firewall porta acesso][cassandra-ports].

- Utilizar a encriptação SSL para todos os [nó de cliente] [ ssl-client-node] e [nó a nó] [ ssl-node-node] comunicações.

- Dentro de uma região, siga as diretrizes [recomendações Cassandra](guidance-compute-n-tier-vm-linux.md#cassandra-recommendations).

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Com uma aplicação de camadas complexa, não poderá ter de criar uma réplica a aplicação completa na região secundário. Em vez disso, poderá de replicadas apenas uma subsystem crítico que é necessário para suportar continuidade do negócio.

É o Gestor de tráfego de um ponto de falha possíveis no sistema. Se o serviço falhar, clientes não consegue aceder a sua aplicação durante o tempo de inatividade. Rever o [Gestor de tráfego SLA][tm-sla]e determine se utilizando o Gestor de tráfego sozinho corresponde às suas necessidades empresariais para elevada disponibilidade. Caso contrário, considere adicionar outra solução de gestão de tráfego como reposição de uma recurso. Se o serviço do Gestor de tráfego Azure falhar, altere os seus registos CNAME no DNS que aponte para outro serviço de gestão de tráfego. (Este passo têm de ser executado manualmente, e a aplicação não estará disponível até que as alterações DNS são propagadas.)

Para Cassandra cluster, os cenários de activação pós-falha a ter em consideração dependem os níveis de consistência utilizados pela aplicação, bem como o número de réplicas utilizado. Para níveis de consistência e a utilização no Cassandra, consulte o artigo [Configurar consistência dos dados] [ cassandra-consistency] e [Cassandra: nós quantos são talked para com quórum?][cassandra-consistency-usage] Disponibilidade de dados no Cassandra é determinada pelo nível de consistência utilizado pela aplicação e o mecanismo de replicação. Para a replicação no Cassandra, consulte o artigo [Replicação de dados no explicação de bases de dados das NoSQL][cassandra-replication].

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Quando atualizar a sua implementação, atualize uma região cada vez, para reduzir as hipóteses de uma falha de global a partir de uma configuração incorreta ou um erro na aplicação.

Teste RDP do sistema para falhas. Eis alguns cenários de falha comuns para testar:

- Encerre instâncias VM.

- Recursos de pressão como memória e CPU.

- Rede de desligar/atraso.

- Processos uma falha de sistema.

- Expire certificados.

- Simule falhas de hardware.

- Encerre do serviço DNS nos controladores de domínio.

Medir as horas de recuperação e certifique-se de que cumpram as necessidades de negócio. Teste as combinações de modos de falha, também.

## <a name="next-steps"></a>Próximos passos

Nesta série tem com o foco nas implementações puro na nuvem. Cenários empresariais com frequência necessitam de uma rede híbrido, que liga uma rede no local com a uma rede virtual Azure. Para saber como criar uma rede de híbridas, consulte [Implementar uma arquitetura de rede híbrida com o Azure e no local VPN][hybrid-vpn].

<!-- Links -->

[azure-sla]: https://azure.microsoft.com/support/legal/sla/
[cassandra-in-azure]: https://academy.datastax.com/resources/deployment-guide-azure
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[cassandra-consistency-usage]: https://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-ports]: http://docs.datastax.com/en/latest-dse/datastax_enterprise/sec/secConfFirePort.html
[datastax]: https://www.datastax.com/products/datastax-enterprise
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[ssl-client-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLClientToNode_t.html
[ssl-node-node]: http://docs.datastax.com/en/cassandra/2.0/cassandra/security/secureSSLNodeToNode_t.html
[tablediff]: https://msdn.microsoft.com/en-us/library/ms162843.aspx
[tm-configure-failover]: ../traffic-manager/traffic-manager-configure-failover-routing-method.md
[tm-monitoring]: ../traffic-manager/traffic-manager-monitoring.md
[tm-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[tm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/traffic-manager/v1_0/
[traffic-manager]: https://azure.microsoft.com/en-us/services/traffic-manager/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[vnet-to-vnet]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[wsfc]: https://msdn.microsoft.com/en-us/library/hh270278.aspx
[0]: ./media/blueprints/compute-multi-dc-linux.png "Arquitetura de rede altamente disponível para as aplicações camadas N Azure"
