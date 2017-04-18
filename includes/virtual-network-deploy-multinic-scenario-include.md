## <a name="scenario"></a>Cenário

Este documento irá guiá-lo através de uma implementação que utiliza vários NIC no VMs num cenário específico. Neste cenário, tem uma duas camadas IaaS carga de trabalho alojada no Azure. Cada camada está implementada no seu próprio sub-rede numa rede virtual (VNet). A camada de front-end é composta por vários servidores da web, agrupados em conjunto num Balanceador de carga definido para alta disponibilidade. A camada de back-end é composta por vários servidores de base de dados. Serão implementados nestes servidores de base de dados com duas NIC cada, uma para acesso de base de dados, a outra para gestão. O cenário também inclui a grupos de segurança de rede (NSGs) para controlar o tráfego que tem permissão para cada sub-rede e NIC na implementação. A figura seguinte mostra a arquitetura básica deste cenário.  

![Cenário de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

