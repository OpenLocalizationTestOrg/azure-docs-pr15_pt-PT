Um balanceador de carga Azure é um balanceador de carga de camada-4 (TCP, UDP). O Balanceador de carga fornece elevada disponibilidade, distribuindo o tráfego de entrada entre instâncias do serviço Saudável nos serviços de nuvem ou máquinas virtuais num conjunto de Balanceador de carga. Balanceador de carga Azure também pode apresentar esses serviços em várias portas, vários endereços IP ou ambos.

Pode configurar um balanceador de carga para:

* Carregar saldo receber tráfego de Internet máquinas virtuais (VMs). Designamos por um balanceador de carga neste cenário um [Balanceador de carga através da Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Tráfego de equilíbrio de carga entre VMs numa rede virtual (VNet), entre VMs nos serviços de nuvem ou entre computadores no local e VMs numa rede virtual entre instalações. Designamos por um balanceador de carga neste cenário um [Balanceador de carga interno (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Reencaminhar tráfego externo a uma instância específica da VM.
