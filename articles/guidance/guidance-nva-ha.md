<properties
   pageTitle="Implementar eletrodomésticos virtuais no elevada disponibilidade | Microsoft Azure"
   description="Como implementar eletrodomésticos virtual de rede na elevada disponibilidade."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>Implementar eletrodomésticos virtuais no elevada disponibilidade

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Este artigo descreve um conjunto de práticas para implementar eletrodomésticos virtual rede (NVAs) de uma forma altamente disponível. Antes de continuar, certifique-se de que a compreender como [rotas definidos pelo utilizador (UDR)] [ udr-overview] e [Balanceador de carga] [ lb-overview] escolar no Azure.

Pode utilizar diferentes NVAs disponíveis no Azure marketplace para aumentar a funcionalidade da Azure da mesma forma que utilizar eletrodomésticos no seu centro de dados no local. A figura seguinte mostra uma implementação de exemplo de uma [única NVA] [ nva-scenario] como um aparelho de firewall. 

![[0]][0]

Apesar da implementação anterior funciona,-apresenta um ponto de falha único. Se o aparelho virtual falhar, os fluxos de nenhum tráfego. Para resolver esse problema, é necessário utilizar várias NVAs. No entanto, que também requer outras definições e recursos para ser utilizado, dependendo das suas necessidades.

Pode utilizar uma das seguintes soluções para implementar um ambiente do NVA altamente disponível.

|Solução|Benefícios|Considerações sobre|
|---|---|---|
|Penetração com eletrodomésticos virtual camada 7|Todos os nós estão ativos|Requer um NVA que pode terminar ligações e utilizar SNAT<br/>Requer um conjunto diferente de NVAs para o tráfego da Internet e a partir do Azure a chegar<br/>Só pode ser utilizado para o tráfego com origem fora do Azure|
|Penetração-saída com eletrodomésticos virtual camada 7|Todos os nós estão ativos<br/>Possa processar tráfego teve originado no Azure |Requer um NVA que pode terminar ligações e utilizar SNAT<br/>Requer um conjunto diferente de NVAs para o tráfego da Internet e a partir do Azure a chegar|
|Mudar de PIP UDR|Conjunto único de NVAs para todo o tráfego<br/>Pode processar todo o tráfego (sem limite regras de portas)|Activo passivo<br/>Requer um processo de activação pós-falha|

## <a name="ingress-with-layer-7-virtual-appliances"></a>Penetração com eletrodomésticos virtual camada 7
Pode utilizar um conjunto de NVAs atrás de um balanceador de carga Azure para fornecer conectividade a das cargas de trabalho Azure atrás de um pequeno conjunto de portas do lado do servidor (como HTTP e HTTPS). A figura seguinte realça como fornecer uma elevada disponibilidade neste cenário ao nível do NVA.

![[1]][1]

Neste cenário, aparelho virtual rede utilizado tem de terminar todas as ligações e passar para a sub-rede carga de trabalho. As máquinas virtuais de carga de trabalho (VMs) responder ao NVA obtido um pedido a partir do e fluxos de tráfego sem problemas. 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>Penetração-saída com eletrodomésticos virtual camada 7
Pode expandir a arquitetura anterior e adicionar outro conjunto de NVAs para processar o tráfego com origem a partir do Azure para serem processados pelo NVAs, conforme apresentado na figura seguinte:

![[2]][2]

Neste cenário, todo o tráfego com origem no Azure é encaminhado para uma Balanceador de carga interno distribui carga entre um conjunto diferente de NVAs. Estes NVAs direcionar tráfego à Internet utilizando os respetivos endereços IP públicos individuais. 

## <a name="pip-udr-switch"></a>Mudar de PIP UDR
Pode evitar criar várias pilhas NVA utilizando duas NVAs no modo de activo passivo. Neste cenário, pode alternar o endereço IP público (PIP) e rotas definidos pelo utilizador (UDRs) Quando interrompe o nó ativo.  

![[3]][3]

Este cenário é semelhante ao cenário NVA único. A única diferença é que a PIP e UDRs tem de ser alteradas para mudar o tráfego entre os NVAs. Estas alterações podem ser feitas manualmente ou, também pode automatizá-los. Para automatizar, pode implementar uma aplicação para ambas as NVAs que verifica se ocorre o estado de funcionamento do nó ativo. Assim que estiver nó activo para baixo, a aplicação pode alterar o PIP e UDRs para criar uma ligação para o nó passivo.

Uma implementação possível desta solução é utilizar uma [ZooKeeper] [ zookeeper] daemon no NVAs para processar eleições políticas de caráter de preenchimento (decidir qual o nó o nó activo). Assim que um caráter de preenchimento é eleito, liga à API REST Azure para remover o PIP de nó falhado e anexe-o para o coordenador. Também-deverá alterar UDRs para apontarem para o endereço IP privado o coordenador novo.

## <a name="next-steps"></a>Próximos passos

- Saiba como [Implementar uma DMZ entre o Azure e o seu centro de dados no local] [ dmz-on-prem] utilizando NVAs camada-7.
- Saiba como [Implementar uma DMZ entre Azure e a Internet] [ dmz-internet] utilizando NVAs camada-7.

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "Arquitetura NVA única"
[1]: ./media/guidance-nva-ha/l7-ingress.png "Penetração camada 7"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "7 a entrada e saída de camadas"
[3]: ./media/guidance-nva-ha/active-passive.png "Cluster activo passivo"