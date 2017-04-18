<properties
   pageTitle="Executar o Windows VMs para uma arquitetura de N camadas | Arquitetura de referência | Microsoft Azure"
   description="Como implementar uma arquitetura de várias camadas no Azure, pagamento uma atenção especial para disponibilidade, segurança, escalabilidade e segurança da capacidade de gestão."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
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

# <a name="running-windows-vms-for-an-n-tier-architecture-on-azure"></a>Executar o Windows VMs para uma arquitetura de camadas no Azure

> [AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

> [AZURE.SELECTOR]
- [Executar Linux VMs para uma arquitetura de camadas no Azure](guidance-compute-n-tier-vm-linux.md)
- [Executar o Windows VMs para uma arquitetura de camadas no Azure](guidance-compute-n-tier-vm.md)

Este artigo descreve um conjunto de provas dadas recomendados para executar o máquinas virtuais do Windows (VMs) para uma aplicação com uma arquitetura de camadas. Cria no artigo [executar várias VMs no Azure][multi-vm].

> [AZURE.NOTE] Azure tem dois modelos de implementação diferente: [Gestor de recursos] [ resource-manager-overview] e clássico. Este artigo utiliza recurso Gestor de designs, a Microsoft recomenda para implementações novas.

## <a name="architecture-diagram"></a>Diagrama de arquitectura

Existem os variações de arquitecturas de camadas. Na maior parte das diferenças não devem indiferente para fins destas recomendações. Este artigo assume uma aplicação web do típica 3 camadas:

- **Camada Web.** Trata os pedidos HTTP recebidos. As respostas são devolvidas através deste camada.

- **Camadas de negócio.** Processos de negócio implementa e outros lógica funcional para o sistema.

- **Camada de base de dados.** Fornece armazenamento de dados persistentes, através do [SQL Server sempre no grupos de disponibilidade] [ sql-alwayson] para elevada disponibilidade.

> Um documento do Visio que inclui este diagrama de arquitectura está disponível para transferência no [Centro de transferências da Microsoft][visio-download]. Este diagrama está ativada "cluster - página de camada (Windows) multi.

![[0]][0]

- **Conjuntos de disponibilidade.** Criar um [Conjunto de disponibilidade] [ azure-availability-sets] para cada camada e aprovisionar VMs, pelo menos, duas em cada camada. Esta abordagem é necessária para alcançar a disponibilidade [SLA] [ vm-sla] para VMs.

- **Sub-redes.** Crie uma sub-rede separada para cada camada. Especifique a endereço intervalo e máscara de sub-rede utilizando a notação [CIDR] . 

- **Carregar balanceadores.** Utilizar um [Balanceador de carga de acesso à Internet] [ load-balancer-external] para distribuir recebido tráfego da Internet para a camada de web e um [Balanceador de carga interno] [ load-balancer-internal] para distribuir o tráfego de rede da camada web para a camada de empresas.

- **Jumpbox**. Um _jumpbox_, também denominado um [anfitrião do contorno], é uma VM na rede em que os administradores utilizam para ligar as outras VMs. O jumpbox tem um NSG que permite que o tráfego apenas de whitelisted endereços IP públicos remoto. O NSG deverá permitir o tráfego de ambiente de trabalho remoto (RDP).

- **Monitorização**. Monitorização de software como [Nagios], [Zabbix]ou [Icinga] pode dar-lhe visão tempo de resposta, tempo de utilização VM e o estado de funcionamento geral do sistema. Instale o software de monitorização numa VM que é colocada numa sub-rede gestão separada.

- **NSGs**. Utilizar [grupos de segurança de rede] [ nsg] (NSGs) para restringir o tráfego de rede dentro de VNet. Por exemplo, na arquitectura 3 camadas aqui apresentada, a camada de base de dados não aceita tráfego a partir do web front-end, apenas a partir da camada de negócio e a sub-rede de gestão.

- **SQL Server sempre no grupo de disponibilidade.** Fornece elevada disponibilidade na camada dados, ao ativar replicação e activação pós-falha.

- **Servidores do active Directory Domain Services (AD DS)**. Serviços de domínio do Active Directory (AD DS) armazena dados de diretório e gere a comunicação entre os utilizadores e domínios, incluindo os processos de início de sessão do utilizador, autenticação e as pesquisas de diretório. Um controlador de domínio do Active Directory é um servidor que executa o AD DS. Antes de Windows Server 2016, sempre no grupos de disponibilidade devem estar associados a um domínio. Isto acontece porque grupos de disponibilidade dependem tecnologia do Windows Server activação pós-falha Cluster (WSFC). Windows Server 2016 apresenta a capacidade de criar um Cluster de activação pós-falha do Active Directory. Para obter mais informações, consulte o artigo [o que é novo no clusters activação pós-falha no Windows Server 2016][wsfc-whats-new]

## <a name="recommendations"></a>Recomendações

Azure oferece vários recursos diferentes e tipos de recursos, para que esta arquitetura de referência pode ser aprovisionado muitas maneiras diferentes. Fornecemos um modelo de Gestor de recursos do Azure para instalar a arquitetura de referência que se segue estas recomendações. Se optar por criar o seu próprio arquitetura de referência seguir estas recomendações exceto se tiver um requisito específico uma recomendação não se ajusta.

### <a name="vnet--subnets"></a>VNet / sub-redes

Quando cria a VNet, alocar suficiente espaço de endereços para sub-redes que terá de. Especifique a VNet intervalo e de sub-rede máscara de endereço utilizando a notação [CIDR] . Utilizar um espaço de endereços que se situar dentro de padrão [blocos de endereço IP privados][private-ip-space], que são 10.0.0.0/8, 172.16.0.0/12 e 192.168.0.0/16.

Selecione um intervalo de endereços que não se sobrepõe à sua rede no local, caso necessite de configurar um gateway entre o VNet e a sua rede no local mais tarde. Depois de criar o VNet, não é possível alterar o intervalo de endereços.

Estruturar sub-redes com os requisitos de funcionalidade e segurança deve ter em conta. Todos os VMs dentro da mesma função ou camada devem ir para a mesma sub-rede, que pode ser um limite de segurança. Para obter mais informações acerca da criação de VNets e sub-redes, consulte o artigo [plano e estrutura redes virtuais Azure][plan-network].

Para cada sub-rede, especifique o espaço de endereços para a sub-rede em notação CIDR. Por exemplo, '10.0.0.0/24' cria um intervalo de 256 endereços IP. (VMs podem utilizar 251 destes; cinco são reservados. Veja a [Rede Virtual FAQ][vnet faq].) Certifique-se dos que intervalos de endereços não fiquem sobrepostas entre sub-redes.

### <a name="network-security-groups"></a>Grupos de segurança de rede

Utilize regras NSG para restringir o tráfego de camadas. Por exemplo, na camada 3 arquitectura mostrada acima, a camada web não comunicar diretamente com a camada de base de dados. Para impor a isto, a camada de base de dados deve bloquear o tráfego de entrada da sub-rede camada web.  

  1. Criar um NSG e associá-lo à sub-rede camada de base de dados.

  2. Adicione uma regra que recusa todo o tráfego entrada a partir do VNet. (Utilizar o `VIRTUAL_NETWORK` etiqueta na regra.) 

  3. Adicione uma regra com uma prioridade mais alta que permite que o tráfego de entrada da sub-rede empresas camada. Esta regra substitui a regra anterior e permite a camada de negócio falar com a camada de base de dados.

  4. Adicione uma regra que permita o tráfego de entrada dentro da sub-rede camada de base de dados própria. Esta regra permite a comunicação entre VMs na camada da base de dados, que é necessário para a replicação de bases de dados e activação pós-falha.

  5. Adicione uma regra que permita tráfego RDP da sub-rede jumpbox. Esta regra permite aos administradores ligar para a camada de base de dados a partir do jumpbox.

  > [AZURE.NOTE] Um NSG tem [regras predefinidas] [ nsg-rules] que permitem a qualquer tráfego entrado a partir do VNet. Não podem ser eliminadas estas regras, mas pode substituí-las ao criar regras de prioridade mais alta.

### <a name="load-balancers"></a>Balanceadores de carga

O Balanceador de carga externos distribuir tráfego da Internet para a camada web. Crie um endereço IP público para este Balanceador de carga. Consulte o artigo [criar um balanceador de carga de acesso à Internet][lb-external-create].

O Balanceador de carga interno distribuir o tráfego de rede da camada web para a camada de empresas. Para dar este Balanceador de carga de um endereço IP privado, crie uma configuração de IP Front-end e associá-lo a sub-rede para a camada de empresas. Consulte o artigo [começar a criar um balanceador de carga interno][lb-internal-create].

### <a name="sql-server-always-on-availability-groups"></a>SQL Server sempre em grupos de disponibilidade

Recomendamos que [Sempre no grupos de disponibilidade] [ sql-alwayson] para elevada disponibilidade do SQL Server. Sempre no grupos de disponibilidade necessitam de um controlador de domínio. Todos os nós no grupo disponibilidade tem de ser o mesmo domínio de AD.

Outras camadas ligar à base de dados através de uma [escuta do grupo de disponibilidade][sql-alwayson-listeners]. A escuta permite que um cliente SQL ligar sem saber o nome da instância do SQL Server física. VMs que devem estar associado a base de dados do access para o domínio. O cliente (neste caso, camada outra) utiliza o DNS para resolver o nome de rede virtual a escuta para endereços IP.

Configure o SQL Server sempre da seguinte forma:

- Crie um cluster de Windows Server activação pós-falha clusters (WSFC) e um grupo de disponibilidade SQL Server sempre no. Para obter mais informações, consulte o artigo [Introdução ao sempre no grupos de disponibilidade][sql-alwayson-getting-started].

- Crie um balanceador de carga interno com um endereço IP estático privado.

- Crie uma escuta de grupo de disponibilidade e mapear nome DNS a escuta para o endereço IP de uma Balanceador de carga interno. 

- Crie uma regra de Balanceador de carga para a porta de monitorização do SQL Server (porta TCP 1433 por predefinição). A regra de Balanceador de carga tem de ativar _IP flutuante_, também denominado devolver directo do servidor. Isto faz com que a VM responder diretamente para o cliente, que permite uma ligação direta para a réplica principal.

    > [AZURE.NOTE] Quando flutuantes IP está ativado, o número da porta front-end tem de ser igual ao número de porta de back-end na regra Balanceador de carga.

Quando tenta ligar-se um cliente de SQL, Balanceador de carga encaminha o pedido de ligação para a réplica que está a página principal atual. Se existir uma activação pós-falha para outra réplica, Balanceador de carga encaminha automaticamente os pedidos subsequentes para a nova réplica principal. Para obter mais informações, consulte o artigo [Configurar Balanceador de carga para SQL sempre no][sql-alwayson-ilb].

Durante uma falha na ligação, ligações de clientes existentes são fechadas. Após completa o activação pós-falha, serão encaminhadas novas ligações para a nova réplica principal.

Se a sua aplicação cometer lê significativamente mais que escreve, pode descarregar algumas das consultas só de leitura para uma réplica secundária. Consulte o artigo [utilizar uma escuta para ligar a um secundário só de leitura réplica (só de leitura encaminhamento)][sql-alwayson-read-only-routing].

Testar a sua implementação por [forçar uma activação manual pós-falha][sql-alwayson-force-failover].

### <a name="jumpbox"></a>Jumpbox

Não permitir acesso RDP da Internet pública para os VMs que executar a carga de trabalho de aplicação. Em vez disso, todo o acesso RDP/SSH estes VMs tem valore a jumpbox. Um administrador os registos para o jumpbox e, em seguida, os registos para a outros VM a partir do jumpbox. O jumpbox permite tráfego RDP da Internet, mas apenas de conhecidos, endereços IP whitelisted.

Coloque o jumpbox no mesmo VNet como as outros VMs, mas numa sub-rede gestão separada.

Crie um [endereço IP público] para o jumpbox.

Utilize um tamanho da memória virtual pequenas para jumpbox, tal como A1 padrão.

Configure os NSGs para a camada web, camada de negócio e sub-redes camada de base de dados para permitir o tráfego de (RDP) administrativo a passar da sub-rede gestão.

Para proteger a jumpbox, crie uma NSG e aplicá-lo à sub-rede jumpbox. Adicione uma regra de NSG que permita ligações de RDP apenas a partir de um conjunto de whitelisted de endereços IP públicos.

Pode ser anexado a NSG à sub-rede ou para a jumpbox NIC. Neste caso, recomendamos que anexar a imagem, para que o tráfego RDP é permitido apenas a jumpbox, mesmo se adicionar outras VMs à mesma sub-rede.

## <a name="availability-considerations"></a>Considerações sobre a disponibilidade

Coloque cada camada ou função VM para um conjunto de disponibilidade em separado. Não coloque VMs a partir de diferentes camadas para o mesmo conjunto de disponibilidade. 

Na camada da base de dados, ter várias VMs não automaticamente traduzir para uma base de dados altamente disponível. Para uma base de dados relacional, normalmente, terá de utilizar a replicação e activação pós-falha para alcançar elevada disponibilidade. Para o SQL Server, recomendamos que utilize [Sempre no grupos de disponibilidade][sql-alwayson]. 

Se tem maior disponibilidade que o [Azure SLA para VMs] [ vm-sla] fornece, criar uma réplica da aplicação ao longo de duas regiões e utilize o Gestor de tráfego de Azure para activação pós-falha. Para obter mais informações, consulte o artigo [Executar VMs Windows em múltiplos regiões para elevada disponibilidade][multi-dc].   

## <a name="security-considerations"></a>Considerações de segurança

Encripte dados at rest. Utilizar o [Azure chave cofre] [ azure-key-vault] para gerir as chaves de encriptação da base de dados. Chave cofre pode armazenar chaves de encriptação nos módulos de segurança do hardware (Os HSMs). Para obter mais informações, consulte [Configurar Azure chave cofre integração para SQL Server no Azure VMs] [ sql-keyvault] também é recomendado para armazenar segredos de aplicação, tais como cadeias de ligação de base de dados, na tecla cofre.

Recomendamos adicionar um aparelho virtual (NVA) para criar uma DMZ entre a Internet pública e a rede virtual Azure de rede. NVA é um termo genérico para uma aplicação virtual que pode efetuar tarefas relacionadas com a rede como firewall, inspeção de pacotes, auditoria, encaminhamento personalizada ou uma variedade de outras operações. Para obter mais informações, consulte o artigo [Implementar uma DMZ entre Azure e a Internet][dmz].

## <a name="scalability-considerations"></a>Considerações de escalabilidade

Balanceadores de carga distribuem o tráfego de rede para as camadas web e empresas. Dimensionar horizontalmente ao adicionar novas instâncias VM. Tenha em atenção que pode dimensionar as camadas web e empresas de forma independente, com base em carregar. Para reduzir possíveis complicações causadas pela oportunidade de manter afinidade do cliente, VMs na camada web devem ser sem estado. Também devem ser sem estado VMs que aloja a lógica empresarial.

## <a name="manageability-considerations"></a>Considerações sobre a capacidade de gestão

Simplificar a gestão de todo o sistema utilizando ferramentas de administração centralizada como [Azure automatização][azure-administration], [Conjunto de aplicações do Microsoft operações gestão][operations-management-suite], [chefe][chef], ou [Puppet][puppet]. Estas ferramentas podem consolidar as informações de diagnóstico e de estado de funcionamento do capturado a partir de várias VMs para fornecem uma visão geral do sistema.

## <a name="solution-deployment"></a>Implementação da solução

Uma implementação para uma arquitetura de referência que implementa estas recomendações está disponível no [Github][github-folder]. Esta arquitetura de referência inclui uma camada web, camada de negócio, uma camada de dados, bem como um jumpbox VM e o Active Directory os controladores de domínio.

A arquitetura de referência pode ser implementada ao seguir as instruções abaixo: 

1. Clique no botão abaixo.  
[! ["Implementar Azure"] [1]][2]

2. Assim que tiver aberto a ligação no portal do Azure, introduza os valores de seguir: 
  - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-ntier-sql-network-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

3. Verifique a notificação de portal Azure para uma mensagem de implementação está concluída.

4. Clique no botão abaixo.  
[! ["Implementar Azure"] [1]][3]

5. Assim que tiver aberto a ligação no portal do Azure, introduza os valores de seguir: 
  - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Utilizar existente** e introduza `ra-ntier-sql-workload-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

6. Verifique a notificação de portal Azure para uma mensagem de implementação está concluída.

7. Clique no botão abaixo.  
[! ["Implementar Azure"] [1]][4]

8. Assim que tiver aberto a ligação no portal do Azure, introduza os valores de seguir: 
  - O nome do **grupo de recursos** já está definido no ficheiro de parâmetro, por isso, selecione **Criar novo** e introduza `ra-ntier-sql-network-rg` na caixa de texto.
  - Selecione a região a partir da caixa pendente **localização** .
  - Não edite o **Modelo de raiz Uri** ou as caixas de texto **Uri de raiz do parâmetro** .
  - Reveja os termos e condições e, em seguida, clique na caixa de verificação **posso concordar com os termos e condições indicadas acima** .
  - Clique no botão **Compra** .

9. Verifique a notificação de portal Azure para uma mensagem de implementação está concluída.

10. Os ficheiros de parâmetro incluem uma codificada nomes de utilizador de administrador e palavras-passe e -se vivamente que imediatamente altere ambos num todos os as VMs. Clique em cada VM no Portal do Azure, em seguida, clique em **Repor palavra-passe** na pá de **suporte + resolução de problemas** . Selecione **Repor palavra-passe** na caixa de lista pendente **modo** , em seguida, selecione um novo **nome de utilizador** e **palavra-passe**. Clique no botão de **atualização** para manter o novo nome de utilizador e palavra-passe. 

Para obter informações sobre formas adicionais de implementar esta arquitetura de referência, consulte o ficheiro Leia-me na [orientação-único-vm] [ github-folder] Github pasta. 

## <a name="next-steps"></a>Próximos passos

Para alcançar elevada disponibilidade para esta arquitetura de referência, recomendamos que [Implementar em várias regiões][multi-dc].

<!-- links -->

[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[azure-administration]: ../automation/automation-intro.md
[azure-audit-logs]: ../resource-group-audit.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-key-vault]: https://azure.microsoft.com/services/key-vault.md
[azure-load-balancer]: ../load-balancer/load-balancer-overview.md
[anfitrião do contorno]: https://en.wikipedia.org/wiki/Bastion_host
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier-sql
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters.md
[multi-vm]: guidance-compute-multi-vm.md
[n-tier]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[endereço IP público]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[sql-alwayson]: https://msdn.microsoft.com/en-us/library/hh510230.aspx
[sql-alwayson-force-failover]: https://msdn.microsoft.com/en-us/library/ff877957.aspx
[sql-alwayson-getting-started]: https://msdn.microsoft.com/en-us/library/gg509118.aspx
[sql-alwayson-ilb]: https://blogs.msdn.microsoft.com/igorpag/2016/01/25/configure-an-ilb-listener-for-sql-server-alwayson-availability-groups-in-azure-arm/
[sql-alwayson-listeners]: https://msdn.microsoft.com/en-us/library/hh213417.aspx
[sql-alwayson-read-only-routing]: https://technet.microsoft.com/en-us/library/hh213417.aspx#ConnectToSecondary
[sql-keyvault]: ../virtual-machines/virtual-machines-windows-ps-sql-keyvault.md
[vm-planned-maintenance]: ../virtual-machines/virtual-machines-windows-planned-maintenance.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[wsfc-whats-new]: https://technet.microsoft.com/windows-server-docs/failover-clustering/whats-new-in-failover-clustering
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/deploy-reference-architecture.sh
[vnet-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/virtualNetwork.parameters.json
[vnet-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/networkSecurityGroups.parameters.json
[nsg-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/networkSecurityGroups.parameters.json
[webtier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/webTier.parameters.json
[webtier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/webTier.parameters.json
[businesstier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/businessTier.parameters.json
[businesstier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/businessTier.parameters.json
[datatier-parameters-windows]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/windows/dataTier.parameters.json
[datatier-parameters-linux]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier/parameters/linux/dataTier.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/compute-n-tier.png "Arquitetura de N camadas utilizando o Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2FvirtualNetwork.azuredeploy.json
[3]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fworkload.azuredeploy.json
[4]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier-sql%2Fsecurity.azuredeploy.json