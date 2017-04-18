<properties
   pageTitle="Vários VIP para Azure Balanceador de carga | Microsoft Azure"
   description="Descrição geral das várias VIP no balanceador de carga Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="chkuhtz"
   manager="narayan"
   editor=""
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="chkuhtz"
/>

# <a name="multiple-vips-for-azure-load-balancer"></a>Balanceador de carga de vários VIP do Azure

Azure Balanceador de carga permite-lhe carregar saldo serviços em múltiplas portas, vários endereços IP ou ambos. Pode utilizar as definições de Balanceador de carga públicas e interna para carregar o saldo monetários através de um conjunto de VMs.

Este artigo descreve os conceitos básicos desta capacidade, conceitos importantes e restrições. Se apenas pretender expor serviços num endereço IP, pode encontrar instruções simplificadas para [públicos](load-balancer-get-started-internet-portal.md) ou [internos](load-balancer-get-started-ilb-arm-portal.md) carregar configurações do Balanceador. Adicionar vários VIP é utilizarão a configuração de um único VIP. Utilizar os conceitos neste artigo, pode expandir uma configuração simplificada em qualquer altura.

Quando define um balanceador de carga Azure, front-end e uma configuração de back-end estão ligados com regras. A sonda de estado de funcionamento referenciada pela regra é utilizada para determinar como as novas monetários são enviadas para um nó no conjunto de back-end. O front-end é definida por um Virtual IP (VIP), que é uma 3-cadeia de identificação composto por um endereço IP (público ou interno), um protocolo de transporte (UDP ou TCP) e um número de porta. Um DIP é um endereço IP numa NIC virtual Azure anexado a uma VM no conjunto de back-end.

A tabela seguinte contém algumas configurações de front-end exemplo:

| VIP | Endereço IP | protocolo | porta |
|-----|------------|----------|------|
|1|65.52.0.1|TCP|80|
|2|65.52.0.1|TCP|_8080_|
|3|65.52.0.1|_UDP RECOMENDADAS_|80|
|4|_65.52.0.2_|TCP|80|

A tabela mostra quatro frontends diferentes. Frontends #1, #2 e #3 são uma única VIP com múltiplas regras. O mesmo endereço IP é utilizado mas a porta ou o protocolo é diferente para cada front-end. Frontends #1 e 4 de # são um exemplo de várias VIP, onde o mesmo frontend protocolo e a porta são reutilizadas de vários VIP.

Azure Balanceador de carga dá flexibilidade no que define as regras de balanceamento de carga. Uma regra declara como um endereço e uma porta no front-end é mapeado para o endereço de destino e a porta no back-end. Se é ou não back-end portas são reutilizadas regras dependem do tipo da regra. Cada tipo de regra tem requisitos específicos que podem afetar a estrutura de configuração e a sonda de anfitrião. Existem dois tipos de regras:

1. A regra predefinida com sem reutilização de porta back-end
2. A regra de IP flutuantes onde são reutilizadas portas back-end

Azure Balanceador de carga permite-lhe misturar os dois tipos de regra na configuração de Balanceador de carga mesmo. O Balanceador de carga pode utilizá-los em simultâneo para uma determinado VM ou qualquer combinação, desde cumpram as restrições da regra. Decidir o tipo de regra que depende os requisitos da aplicação e a complexidade do que a configuração de suporte. Deve avaliar quais os tipos de regra são mais adequados para o seu cenário.

Vamos explorar estes cenários ainda mais ao começar com o comportamento predefinido.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regra de tipo de #1: sem reutilização de porta back-end

![Ilustração de MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Neste cenário, VIP frontend é configurado da seguinte forma:

| VIP | Endereço IP | protocolo | porta |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

DIP é o destino do fluxo de entrada. No conjunto de back-end, cada VM expõe o serviço numa porta exclusivo num DIP pretendido. Este serviço está associado a front-end através de uma definição de regra.

Vamos definir duas regras:

| Regra | Mapear front-end | Para conjunto de dados back-end |
|------|--------------|-----------------|
| 1 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

O mapeamento concluído no Azure Balanceador de carga está agora da seguinte forma:

| Regra | Endereço VIP IP | protocolo | porta | Destino | porta |
|------|----------------|----------|------|-----|------|
|![regra](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|Endereço IP DIP|80|
|![regra](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|Endereço IP DIP|81|

Cada regra tem de produzir um fluxo de com uma combinação exclusivo de endereço IP de destino e porta de destino. Por diversas porta de destino do fluxo da, múltiplas regras podem entregar monetários ao mesmo DIP portas diferentes.

Estado de funcionamento sondas sempre são dirigidas a DIP de uma VM. Tem de se certificar de que seu sonda reflete o estado de funcionamento da VM.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regra de tipo de #2: reutilização de porta de back-end utilizando IP flutuantes

Azure Balanceador de carga fornece a flexibilidade de poder reutilizar a porta de front-end através de vários VIP independentemente do tipo de regra utilizada. Para além disso, alguns cenários de aplicação preferem ou exigem a mesma porta para ser utilizado por várias instâncias de aplicação numa única VM no conjunto de back-end. Exemplos comuns de reutilização de porta incluem clusters para elevada disponibilidade, eletrodomésticos virtuais e expor múltiplos TLS pontos finais sem encriptação reduções de rede.

Se pretender reutilizar a porta de back-end através de múltiplas regras, tem de ativar flutuantes IP na definição do regra.

Flutuantes IP são uma parte de que é conhecido como direta servidor devolvido DSR (). DSR consiste em duas partes: uma topologia de fluxo e um endereço IP esquema de mapeamento. A um nível de plataforma, Balanceador de carga Azure funciona sempre uma topologia de fluxo DSR, independentemente de se o flutuante IP está ativado ou não. Isto significa que a saída parte de um fluxo de é sempre corretamente reprogramada fluxo diretamente para a origem.

Com o tipo de regra predefinido, Azure expõe um esquema de mapeamento do endereço IP para fácil utilização de balanceamento de carga tradicional. Activar flutuantes IP altera o esquema de mapeamento de endereços IP para permitir para flexibilidade adicional, conforme explicado abaixo.

O diagrama seguinte ilustra esta configuração:

![Ilustração de MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Para este cenário, cada VM no conjunto de back-end tem três interfaces de rede:

* DIP: NIC Virtual associada a VM (do Azure NIC de recurso)
* VIP1: uma interface de loopback dentro de convidado SO está configurado com endereço IP do VIP1
* VIP2: uma interface de loopback dentro de convidado SO está configurado com endereço IP do VIP2

>[AZURE.IMPORTANT] A configuração das interfaces lógicas é realizada dentro de convidado SO. Esta configuração não é executada ou gerida pelo Azure. Nesta configuração, as regras não irão funcionar. Definições do Estado de funcionamento sonda utilizam DIP da VM em vez do VIP lógico. Por conseguinte, o serviço tem de fornecer respostas sonda numa porta de DIP refletir o estado do serviço oferecido sobre o VIP lógico.

Vamos assumir a mesma configuração de front-end, tal como o cenário anterior:

| VIP | Endereço IP | protocolo | porta |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

Vamos definir duas regras:

| Regra | Mapear front-end | Para conjunto de dados back-end |
|------|--------------|-----------------|
| 1 | ![regra](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 (em VM1 e VM2) |
| 2 | ![regra](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 (em VM1 e VM2) |

A tabela seguinte mostra o mapeamento concluído no balanceador de carga:

| Regra | Endereço VIP IP | protocolo | porta | Destino | porta |
|------|----------------|----------|------|-------------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|mesmo que VIP (65.52.0.1)|mesmo que VIP (80)|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|mesmo que VIP (65.52.0.2)|mesmo que VIP (80)|

O destino do fluxo de entrada é o endereço VIP na interface de loopback na VM. Cada regra tem de produzir um fluxo de com uma combinação exclusivo de endereço IP de destino e porta de destino. Por variável o endereço IP de destino do fluxo da, reutilização de porta é possível sobre a mesma VM. O serviço é exposto ao balanceador de carga ligando-o para o VIP endereço IP e portas da interface do loopback respetivos.

Repare que neste exemplo não altera a porta de destino. Apesar de este é um cenário de IP flutuante, Balanceador de carga Azure também suporta a definir uma regra para reescrever a porta de destino de back-end e para que seja diferente da porta de destino de front-end.

O tipo de regra de IP flutuantes é a Fundação de várias padrões de configuração do Balanceador de carga. Um exemplo que se encontra atualmente disponível é a configuração do [SQL AlwaysOn com vários Listeners](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Ao longo do tempo, irá-se documentados mais destes cenários.

## <a name="limitations"></a>Limitações

* Várias configurações VIP só são suportadas com IaaS VMs.
* Com a regra de IP flutuante, a aplicação tem de utilizar DIP fluxos de saída dos. Se a sua aplicação liga-se ao endereço VIP configurado na interface de loopback no sistema operativo convidado, em seguida, SNAT não está disponível para reescrever o fluxo de saída e o fluxo de falha.
* Endereços IP públicos têm um efeito de faturação. Para obter mais informações, consulte o artigo [preços do endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Aplicam limites de subscrição. Para mais informações, consulte o artigo [limites de serviço](../azure-subscription-service-limits.md#networking-limits) para obter detalhes.
