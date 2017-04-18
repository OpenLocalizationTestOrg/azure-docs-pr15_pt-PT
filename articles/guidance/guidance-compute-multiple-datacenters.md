<properties
   pageTitle="A executar o Windows VMs em várias regiões para elevada disponibilidade | Arquitetura de referência | Microsoft Azure"
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

# <a name="running-windows-vms-in-multiple-regions-for-high-availability"></a>A executar o Windows VMs em várias regiões para elevada disponibilidade

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [A ser executada Linux VMs em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters-linux.md)
- [A executar o Windows VMs em várias regiões para elevada disponibilidade](guidance-compute-multiple-datacenters.md)

Neste artigo, recomendamos que um conjunto de práticas para executar máquinas virtuais do Windows (VMs) nas regiões Azure múltiplos, para alcançar a disponibilidade e uma infraestrutura de recuperação de falhas robusta.

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource groups] e clássico. Este artigo utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

Uma arquitetura de região com várias pode fornecer maior disponibilidade que implementar a uma única região. Se uma falha de regional afetar a região principal, pode utilizar o [Gestor de tráfego] [ traffic-manager] Falha ao longo à região de secundário. Também pode ajudar a esta arquitetura se falhar um subsystem individual da aplicação. 

Existem várias abordagens gerais para alcançar elevada disponibilidade nos centros de dados:

- Activo/passivo com quente em espera. Vai tráfego para uma região, enquanto o aguarda até em modo de espera. VMs na região secundário são atribuídos e a execução sempre.

- Activo/passivo com suspensão fria. O mesmo, mas VMs na região secundário não são atribuídos até necessário para activação pós-falha. Esta abordagem custos menor a executar, mas geralmente terão de já vez durante uma falha.

- Activo/activo. Ambas as regiões estão ativas e os pedidos de são distribuído entre elas. Se um centro de dados ficar disponível, é assumido terminar rotação. 

Esta arquitetura foca-se em activo/passivo com suspensão quente, utilizando o Gestor de tráfego para activação pós-falha. Tenha em atenção que poderia implementar um pequeno número de VMs para suspensão quente e, em seguida, dimensionar saída, conforme necessário.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

O diagrama seguinte cria na arquitetura apresentada na [fiabilidade de adicionar a uma arquitetura de camadas no Azure](guidance-compute-n-tier-vm.md).

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está ativada "cluster - página de região (Windows) multi.

[! [0]][0]

- **Regiões principais e secundários**. Esta arquitetura utiliza duas regiões para alcançar maior disponibilidade. Uma é a região principal. Durante as operações normais, o tráfego de rede é encaminhado para a região principal. Mas se que ficar disponível, o tráfego é encaminhado para a região secundária.

- ** [Azure tráfego Gestor] [ traffic-manager] ** encaminha pedidos recebidos à região de principal. Se esse região ficar disponível, o Gestor de tráfego Falha ao longo à região de secundário. Para mais informações, consulte a secção [Configurar o Gestor de tráfego](#configuring-traffic-manager).

- **Grupos de recursos**. Crie separados [grupos de recursos] [ resource groups] para a região primária, secundário região e para o Gestor de tráfego. Isto dá flexibilidade para gerir o cada região como um único conjunto de recursos. Por exemplo, pode implementar uma região, sem ser para baixo da outra. [Ligar os grupos de recursos][resource-group-links], para que pode executar uma consulta para listar todos os recursos para a aplicação.

- **VNets**. Crie um VNet separada para cada região. Certifique-se de que não se sobrepõem os espaços de endereço. 

- **SQL Server sempre num grupo de disponibilidade**. Se estiver a utilizar o SQL Server, recomendamos [SQL sempre no Availabilty grupos] [ sql-always-on] para elevada disponibilidade. Crie um grupo de única disponibilidade que inclui as instâncias do SQL Server em ambas as regiões. Para mais informações, consulte a secção [configurar o SQL Server sempre no disponibilidade grupo](#configuring-the-sql-server-alwayson-availability-group ).

> [AZURE.NOTE] Considere também a [Base de dados do SQL Azure][azure-sql-db], que fornece uma base de dados relacional como um serviço na nuvem. Base de dados SQL, não precisa de configurar um grupo de disponibilidade ou gerir activação pós-falha.  

- **Os Gateways de VPN**: criar um [VPN gateway] [ vpn-gateway] em cada VNet e configurar uma [ligação de VNet para VNet][vnet-to-vnet], para permitir o tráfego de rede entre as duas VNets. Isto é necessário para o grupo de disponibilidade SQL sempre no.

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

### <a name="sql-server-always-on-configuration"></a>Configuração do SQL Server sempre no

SQL Server sempre no grupos de disponibilidade necessitam de um controlador de domínio. Todos os nós no grupo disponibilidade tem de ser o mesmo domínio de AD. Os seguintes pontos de fornecem orientações relativas à como configurar um grupo de disponibilidade SQL Server sempre no:

- No mínimo, coloque dois controladores de domínio em cada região. 

- Dar um endereço IP estático de cada controlador de domínio.

- Crie uma ligação de VNet para VNet para permitir a comunicação entre o VNets.

- Para cada VNet, adicione os endereços IP os controladores de domínio (a partir de ambas as regiões) para a lista de servidor de DNS. Pode utilizar o seguinte comando clip. Obter mais informações, consulte o artigo [Gerir DNS servers utilizados por uma rede virtual (VNet)][vnet-dns].

```bat
azure network vnet set --resource-group dc01-rg --name dc01-vnet --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
```

- Criar um [Clusters de activação pós-falha do Windows Server] [ wsfc] cluster de (WSFC) que inclua instâncias do SQL Server em ambas as regiões. 

- Crie um grupo de disponibilidade ' SQL Server sempre no ' que inclui as instâncias do SQL Server em ambas as regiões principais e secundárias. Consulte o artigo [Prolongamento sempre no grupo de disponibilidade ao centro de dados Azure remoto (PowerShell)](https://blogs.msdn.microsoft.com/sqlcat/2014/09/22/extending-alwayson-availability-group-to-remote-azure-datacenter-powershell/) para obter os passos. 

- Coloca a réplica principal na região principal.

- Coloca um ou mais réplicas secundárias na região principal. Configure estas para utilizar consolidar síncrono com activação pós-falha automática.

- Coloca um ou mais réplicas secundárias na região secundário. Configure estas utilizar *assíncrona* consolidação, por motivos de desempenho. (Caso contrário, todas as operações de SQL tem de esperar numa viagem round através da rede à região de secundário.) 

> [AZURE.NOTE] Réplicas consolidar assíncrona não suportam activação pós-falha automática. 

Para obter mais informações, consulte [a executar o Windows VMs para uma arquitetura de camadas no Azure](guidance-compute-n-tier-vm.md#SQL-AlwaysOn-Availability-Group).

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Com uma aplicação de camadas complexa, não poderá ter de criar uma réplica a aplicação completa na região secundário. Em vez disso, poderá de replicadas apenas uma subsystem crítico que é necessário para suportar continuidade do negócio.

É o Gestor de tráfego de um ponto de falha possíveis no sistema. Se o serviço falhar, clientes não consegue aceder a sua aplicação durante o tempo de inatividade. Rever o [Gestor de tráfego SLA][tm-sla]e determine se utilizando o Gestor de tráfego sozinho corresponde às suas necessidades empresariais para elevada disponibilidade. Caso contrário, considere adicionar outra solução de gestão de tráfego como reposição de uma recurso. Se o serviço do Gestor de tráfego Azure falhar, altere os seus registos CNAME no DNS que aponte para outro serviço de gestão de tráfego. (Este passo têm de ser executado manualmente, e a aplicação não estará disponível até que as alterações DNS são propagadas.) 

Para o cluster de SQL Server, existem dois cenários activação pós-falha a ter em conta:

1. Todas as réplicas SQL na região primária falharem. Por exemplo, isto pode acontecer durante uma falha de regional. Nesse caso, qual tem manualmente falhar ao longo do grupo de disponibilidade de SQL, apesar de Gestor de tráfego falha automaticamente sobre no front-end. Siga os passos em [executar um forçada Manual activação pós-falha de um grupo de disponibilidade do SQL Server](https://msdn.microsoft.com/library/ff877957.aspx), que descreve como efetuar uma activação forçada pós-falha ao utilizar o SQL Server Management Studio, Transact-SQL ou PowerShell no SQL Server 2016. 

    > [AZURE.WARNING] Com forçada falha na ligação, não existe um risco de perda de dados. Assim que a região principal estiver novamente online, tire um instantâneo da base de dados e utilizar [tablediff] para localizar as diferenças.

2. Gestor de tráfego Falha ao longo à região de secundário, mas a réplica SQL principal ainda se encontra disponível. Por exemplo, a camada front-end poderá falhar, sem afetar o VMs de SQL. Nesse caso, o tráfego da Internet é encaminhado para a região secundária e dessa região ainda pode ligar-se para a réplica SQL principal. No entanto, haverá um aumento latência, uma vez que as ligações de SQL vão-se entre as regiões. Neste caso, deverá efectuar uma activação manual pós-falha da seguinte forma: 

    - Mude temporariamente uma réplica SQL na região secundário para consolidar *síncrono* . Isto garante que não haverá perda de dados durante a mudança de.

    - Sobre a falhar que a réplica SQL. 
    
    - Quando voltar a região primária falhar, restaure a definição de consolidação assíncrona. 

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
[azure-sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[health-endpoint-monitoring-pattern]: https://msdn.microsoft.com/library/dn589789.aspx
[hybrid-vpn]: guidance-hybrid-network-vpn.md
[regional-pairs]: ../best-practices-availability-paired-regions.md
[resource groups]: ../azure-resource-manager/resource-group-overview.md
[resource-group-links]: ../resource-group-link-resources.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[sql-always-on]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
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
[0]: ./media/blueprints/compute-multi-dc.png "Arquitetura de rede altamente disponível para as aplicações camadas N Azure"