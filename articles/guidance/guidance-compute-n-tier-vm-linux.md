<properties
   pageTitle="Executar Linux VMs para uma arquitetura de camadas no Azure | Microsoft Azure"
   description="Como executar Linux VMs para uma arquitetura de camadas N no Microsoft Azure."
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
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-for-an-n-tier-architecture-on-azure"></a>Executar Linux VMs para uma arquitetura de camadas no Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]


> [AZURE.SELECTOR]
- [Executar Linux VMs para uma arquitetura de camadas no Azure](guidance-compute-n-tier-vm-linux.md)
- [Executar o Windows VMs para uma arquitetura de camadas no Azure](guidance-compute-n-tier-vm.md)

Este artigo descreve um conjunto de provas dadas recomendados para executar o máquinas virtuais do Linux (VMs) para uma aplicação com uma arquitetura de camadas. Cria no artigo [executar várias VMs no Azure][multi-vm].

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Este artigo utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

Existem os variações de arquitecturas de camadas. Na maior parte das diferenças não devem indiferente para fins destas recomendações. Este artigo assume uma aplicação web do típica 3 camadas:

- **Camada Web.** Trata os pedidos HTTP recebidos. As respostas são devolvidas através deste camada.

- **Camadas de negócio.** Processos de negócio implementa e outros lógica funcional para o sistema.

- **Camada de base de dados.** Fornece armazenamento de dados persistentes, utilizando Apache Cassandra para elevada disponibilidade.

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está ativada "cluster - página de camada (Linux) multi.

![[0]][0]

- **Conjuntos de disponibilidade.** Criar um [Conjunto de disponibilidade] [ azure-availability-sets] para cada camada e aprovisionar VMs, pelo menos, duas em cada camada. Esta abordagem é necessária para alcançar a disponibilidade [SLA] [ vm-sla] para VMs.

- **Sub-redes.** Crie uma sub-rede separada para cada camada. Especifique a endereço intervalo e máscara de sub-rede utilizando a notação [CIDR] . 

- **Carregar balanceadores.** Utilizar um [Balanceador de carga de acesso à Internet] [ load-balancer-external] para distribuir recebido tráfego da Internet para a camada de web e um [Balanceador de carga interno] [ load-balancer-internal] para distribuir o tráfego de rede da camada web para a camada de empresas.

- **Jumpbox**. Um _jumpbox_, também denominado um [anfitrião do contorno], é uma VM na rede em que os administradores utilizam para ligar as outras VMs. O jumpbox tem um NSG que permite que o tráfego apenas de whitelisted endereços IP públicos remoto. O NSG deverá permitir o tráfego de shell seguro (SSH).

- **Monitorização**. Monitorização de software como [Nagios], [Zabbix]ou [Icinga] pode dar-lhe visão tempo de resposta, tempo de utilização VM e o estado de funcionamento geral do sistema. Instale o software de monitorização numa VM que é colocada numa sub-rede gestão separada.

- **NSGs**. Utilizar [grupos de segurança de rede] [ nsg] (NSGs) para restringir o tráfego de rede dentro de VNet. Por exemplo, na arquitectura 3 camadas aqui apresentada, a camada de base de dados não aceita tráfego a partir do web front-end, apenas a partir da camada de negócio e a sub-rede de gestão.

- **Apache Cassandra base de dados**. Fornece elevada disponibilidade na camada dados, ao ativar replicação e activação pós-falha.

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar o seu próprio arquitetura de referência seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="vnet--subnets"></a>VNet / sub-redes

Quando cria a VNet, alocar suficiente espaço de endereços para sub-redes que terá de. Especifique a VNet intervalo e de sub-rede máscara de endereço utilizando a notação [CIDR] . Utilizar um espaço de endereços que se situar dentro de padrão [blocos de endereço IP privados][private-ip-space], que são 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Selecione um intervalo de endereços que não se sobrepõe à sua rede no local, caso necessite de configurar um gateway entre o VNet e a sua rede no local mais tarde. Depois de criar o VNet, não é possível alterar o intervalo de endereços.

Estruturar sub-redes com os requisitos de funcionalidade e segurança deve ter em conta. Todos os VMs dentro da mesma função ou camada devem ir para a mesma sub-rede, que pode ser um limite de segurança. Para obter mais informações acerca da criação de VNets e sub-redes, consulte o artigo [plano e estrutura redes virtuais Azure][plan-network].

Para cada sub-rede, especifique o espaço de endereços para a sub-rede em notação CIDR. Por exemplo, '10.0.0.0/24' cria um intervalo de 256 endereços IP. (VMs podem utilizar 251 destes; cinco são reservados. Veja a [Rede Virtual FAQ][vnet faq].) Certifique-se dos que intervalos de endereços não fiquem sobrepostas entre sub-redes.

### <a name="load-balancers"></a>Balanceadores de carga

O Balanceador de carga externos distribuir tráfego da Internet para a camada web. Crie um endereço IP público para este Balanceador de carga. Consulte o artigo [criar um balanceador de carga de acesso à Internet][lb-external-create].

O Balanceador de carga interno distribuir o tráfego de rede da camada web para a camada de empresas. Para dar este Balanceador de carga de um endereço IP privado, crie uma configuração de IP Front-end e associá-lo a sub-rede para a camada de empresas. Consulte o artigo [começar a criar um balanceador de carga interno][lb-internal-create].

### <a name="cassandra"></a>Cassandra

Recomendamos que [DataStax Enterprise] [ datastax] para produção utilização, mas estas recomendações aplicam-se para qualquer edição Cassandra. Para obter mais informações sobre como executar DataStax no Azure, consulte o artigo [Guia de implementação de empresa DataStax para Azure][cassandra-in-azure]. 

Coloque VMs para um cluster de Cassandra num conjunto de disponibilidade, para garantir que as réplicas Cassandra são distribuídas por vários domínios falhas e actualizar domínios. Para mais informações sobre domínios falhas e atualização, consulte o artigo [Gerir a disponibilidade de máquinas virtuais][availability-sets-manage]. 

Configure domínios do falhas 3 (o máximo) por disponibilidade conjunto. 

Configure domínios atualização 18 por disponibilidade conjunto. Isto dá-lhe o número máximo de atualização domínios que ainda podem ser distribuídas uniformemente ao longo dos domínios falhas.   

Configure nós no modo de deverá ter em consideração bastidor. Mapear domínios falhas para esquis na `cassandra-rackdc.properties` ficheiro.

Não precisa de um balanceador de carga à frente do cluster. O cliente liga diretamente para um nó no cluster.

### <a name="jumpbox"></a>Jumpbox

Coloque o jumpbox no mesmo VNet como as outros VMs, mas numa sub-rede gestão separada.

Crie um [endereço IP público] para o jumpbox.

Utilize um tamanho da memória virtual pequenas para jumpbox, tal como A1 padrão.

Configure os NSGs para a camada web, camada de negócio e sub-redes camada de base de dados para permitir o tráfego de (SSH) administrativo a passar da sub-rede gestão.

Para proteger a jumpbox, crie uma NSG e aplicá-lo à sub-rede jumpbox. Adicione uma regra de NSG que permita ligações de SSH apenas a partir de um conjunto de whitelisted de endereços IP públicos.

Pode ser anexado a NSG à sub-rede ou para a jumpbox NIC. Neste caso, recomendamos que anexar a imagem, para que SSH é permitido apenas ao tráfego jumpbox, mesmo se adicionar outras VMs à mesma sub-rede.

Não permitir acesso SSH da Internet pública para os VMs que executar a carga de trabalho de aplicação. Em vez disso, todo o acesso SSH estes VMs tem valore a jumpbox. Um administrador os registos para o jumpbox e, em seguida, os registos para a outros VM a partir do jumpbox. O jumpbox permite SSH tráfego da Internet, mas apenas de conhecidos, endereços IP whitelisted.

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Coloque cada camada ou função VM para um conjunto de disponibilidade em separado. Não coloque VMs a partir de diferentes camadas para o mesmo conjunto de disponibilidade. 

Na camada da base de dados, ter várias VMs não automaticamente traduzir para uma base de dados altamente disponível. Para uma base de dados relacional, normalmente, terá de utilizar a replicação e activação pós-falha para alcançar elevada disponibilidade.  

Se tem maior disponibilidade que o [Azure SLA para VMs] [ vm-sla] fornece, criar uma réplica da aplicação ao longo de duas regiões e utilize o Gestor de tráfego de Azure para activação pós-falha. Para obter mais informações, consulte o artigo [Executar VMs Linux em várias regiões para elevada disponibilidade][multi-dc].  

## <a name="security-considerations"></a>Considerações de segurança

Utilize regras NSG para restringir o tráfego de camadas. Por exemplo, na camada 3 arquitectura mostrada acima, a camada web não comunicar diretamente com a camada de base de dados. Para impor a isto, a camada de base de dados deve bloquear o tráfego de entrada da sub-rede camada web.  

  1. Criar um NSG e associá-lo à sub-rede camada de base de dados.

  2. Adicione uma regra que recusa todo o tráfego entrada a partir do VNet. (Utilizar o `VIRTUAL_NETWORK` etiqueta na regra.) 

  3. Adicione uma regra com uma prioridade mais alta que permite que o tráfego de entrada da sub-rede empresas camada. Esta regra substitui a regra anterior e permite a camada de negócio falar com a camada de base de dados.

  4. Adicione uma regra que permita o tráfego de entrada dentro da sub-rede camada de base de dados própria. Esta regra permite a comunicação entre VMs na camada da base de dados, que é necessário para a replicação de bases de dados e activação pós-falha.

  5. Adicione uma regra que permita SSH tráfego da sub-rede jumpbox. Esta regra permite aos administradores ligar para a camada de base de dados a partir do jumpbox.

  > [AZURE.NOTE] Um NSG tem [regras predefinidas] [ nsg-rules] que permitem a qualquer tráfego entrado a partir do VNet. Não podem ser eliminadas estas regras, mas pode substituí-las ao criar regras de prioridade mais alta.

Recomendamos adicionar um aparelho virtual (NVA) para criar uma DMZ entre a Internet pública e a rede virtual Azure de rede. NVA é um termo genérico para uma aplicação virtual que pode efetuar tarefas relacionadas com a rede como firewall, inspeção de pacotes, auditoria, encaminhamento personalizada ou uma variedade de outras operações. Para obter mais informações, consulte o artigo [Implementar uma DMZ entre Azure e a Internet][dmz].

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Balanceadores de carga distribuem o tráfego de rede para as camadas web e empresas. Dimensionar horizontalmente ao adicionar novas instâncias VM. Tenha em atenção que pode dimensionar as camadas web e empresas de forma independente, com base em carregar. Para reduzir possíveis complicações causadas pela oportunidade de manter afinidade do cliente, VMs na camada web devem ser sem estado. Também devem ser sem estado VMs que aloja a lógica empresarial.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Simplificar a gestão de todo o sistema utilizando ferramentas de administração centralizada como [Azure automatização][azure-administration], [Conjunto de aplicações do Microsoft operações gestão][operations-management-suite], [chefe][chef], ou [Puppet][puppet]. Estas ferramentas podem consolidar as informações de diagnóstico e de estado de funcionamento do capturado a partir de várias VMs para fornecem uma visão geral do sistema.

## <a name="solution-deployment"></a>Implementação da solução

Uma implementação para uma arquitetura de referência que implementa estas recomendações está disponível no [Github][github-folder]. Esta arquitetura de referência inclui uma camada de web, camada de negócio e uma camada de dados.

1. Clique no botão abaixo.  
[! ["Implementar Azure"] [1]][2]

2. Assim que tiver aberto a ligação no portal do Azure, introduza os valores de seguir: 
  - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-ntier-sql-network-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

3. Verifique a notificação de portal Azure para uma mensagem de implementação está concluída.

4. Os ficheiros de parâmetro incluem uma codificada nomes de utilizador de administrador e palavras-passe e -se vivamente que imediatamente altere ambos num todos os as VMs. Clique em cada VM no Portal do Azure, em seguida, clique em **Repor palavra-passe** na pá de **suporte + resolução de problemas** . Selecione **Repor palavra-passe** na caixa de lista pendente **modo** , em seguida, selecione um novo **nome de utilizador** e **palavra-passe**. Clique no botão de **atualização** para manter o novo nome de utilizador e palavra-passe.

## <a name="next-steps"></a>Próximos passos

Para alcançar elevada disponibilidade para esta arquitetura de referência, recomendamos que [Implementar em várias regiões][multi-dc].

<!-- links -->

[azure-administration]: ../automation/automation-intro.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[availability-sets-manage]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[anfitrião do contorno]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-consistency-usage]: http://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-in-azure]: https://docs.datastax.com/en/datastax_enterprise/4.5/datastax_enterprise/install/installAzure.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters-linux.md
[multi-vm]: guidance-compute-multi-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[endereço IP público]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./media/blueprints/compute-n-tier-linux.png "Arquitetura de N camadas utilizando o Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier%2Fazuredeploy.json


