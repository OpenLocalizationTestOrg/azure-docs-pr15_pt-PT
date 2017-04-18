<properties
 pageTitle="Tamanhos dos serviços em nuvem | Microsoft Azure"
 description="Lista os tamanhos de máquina virtual diferente (e ID) para funções de web e trabalhador do serviço de nuvem Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Tamanhos dos serviços em nuvem

Este tópico descreve as opções para instâncias de função do serviço na nuvem (funções da web e funções de trabalho) e tamanhos disponíveis. Também fornece considerações de implementação do ter em consideração ao planear utilizar estes recursos. Cada tamanho tem um ID que irá colocar no seu [ficheiro de definição de serviço](cloud-services-model-and-package.md#csdef).

Serviços em nuvem é um dos vários tipos de recursos de cluster disponibilizados pelos Azure. Clique [aqui](cloud-services-choose-me.md) para obter mais informações sobre os serviços na nuvem.

> [AZURE.NOTE]Para ver limites Azure relacionados, consulte a [subscrição do Azure e limites de serviço, Quotas e restrições](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamanhos de web e trabalhador instâncias de função

Existem vários tamanhos padrão para escolher no Azure. Considerações para algumas destes tamanhos incluem:

* D série VMs foram concebidos para executar as aplicações que procura mais elevado cluster power e o desempenho do disco temporário. D série VMs fornecem processadores mais rápidos, um rácio de memória-para-core mais elevado e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, consulte o artigo o anúncio no blogue do Azure, [Novos tamanhos de Máquina Virtual D série](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Série de Dv2, um sucessor para as séries de D originais, funcionalidades de uma CPU mais eficazes. A CPU Dv2 série é cerca 35% mais rápido do que a CPU D série. Baseia-se a última geração 2,4 GHz Intel Xeon® E5-2673 v3 processador (Haswell) e com o Intel Turbo intensidade tecnologia 2.0, pode aceder até 3.1 GHz. A série de Dv2 tem as configurações de memória e de disco mesmo como a série de D.

*   Série G VMs oferecem mais memória e executar no anfitriões têm processadores da família Intel Xeon E5 V3.

*   Podem ser implementadas VMs a série A uma variedade de tipos de hardware e processadores. O tamanho encontra-se limitada, baseadas hardware, para oferecer o desempenho do processador consistente para a instância em execução, independentemente do hardware que for implementado no. Para determinar o hardware físico no qual este tamanho for implementado, consulta o hardware virtual a partir de Máquina Virtual.

*   O tamanho de A0 é indevidamente subscrito o hardware física. Para este tamanho específico apenas, outras implementações do cliente podem afetar o desempenho da sua carga de trabalho em execução. O desempenho relativo está descrito abaixo como do plano base esperado sujeito a uma variabilidade aproximado de 15%.


O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de armazenamento, memória e processamento de máquina virtual. Custos de armazenamento são calculados separadamente com base nas páginas utilizadas na conta de armazenamento. Para obter detalhes, consulte o artigo [Máquinas virtuais preços detalhes](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 


As seguintes considerações podem ajudar a decidir num tamanho:


* Os tamanhos A8 A11 e H série também são conhecidos como *com muitos cluster instâncias*. O hardware que é executada estes tamanhos concebido e otimizado para cluster-a com um grau elevado e aplicações de rede-a com um grau elevado, incluindo computação de alto desempenho (HPC) cluster simulações, modelação e aplicações. A série de A8 A11 utiliza Intel Xeon E5-2670 @ 2.6 GHZ e a série de H utiliza Intel Xeon E5-2667 v3 @ 3.2 GHz. Para obter informações detalhadas e considerações sobre como utilizar estes tamanhos, consulte o artigo [sobre VMs de série de uma série de H e cluster-a com um grau elevado](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 

* Série de Dv2, D-série, G-série, são ideais para as aplicações que procura CPUs mais rápidas, local melhor desempenho do disco ou se tiver mais elevados pedidos de memória.  Estas funcionalidades oferecem uma poderosa combinação para muitas aplicações empresarial.

*   Alguns dos anfitriões físicos nos centros de dados Azure poderão não suportam tamanhos de máquina virtual maiores, tais como A5 – A11. Como resultado, poderá ver a mensagem de erro **falhou para configurar máquina virtual {nome do computador}** ou **não foi possível criar máquina virtual {nome do computador}** quando o redimensionamento uma máquina de virtual existente para um novo tamanho; criar uma nova máquina virtual numa rede virtual criada antes de 16 de Abril de 2013; ou ao adicionar uma nova máquina virtual a um serviço de nuvem existente. Consulte o artigo [erro: "Falha ao configurar máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte para obter soluções para cada cenário de implementação.  

* A sua subscrição também poderá limitar o número de núcleos que pode implementar em determinadas famílias de tipos de tamanho. Para aumentar uma quota, contacte o suporte do Azure.


## <a name="performance-considerations"></a>Considerações sobre o desempenho

Criámos o conceito do Azure calcular unidade (ACU) para fornecer uma forma de comparação de desempenho de cluster (CPU) ao longo do Azure SKUs. Isto irá ajudar a identificar facilmente que SKU é provavelmente satisfazer as suas necessidades de desempenho.  ACU está atualmente padronizado um pequeno (Standard_A1) VM a ser 100 e todos os SKUs, em seguida, representa aproximadamente quanto mais rápido que SKU pode ser executado uma padrão de referência. 

>[AZURE.IMPORTANT] O ACU é apenas uma orientação.  Resultados da sua carga de trabalho podem variar. 

<br>

|Família SKU |ACU/Core |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5-7](#a-series) |100 |
|[A8 A11](#a-series)    |225 *|
|[D1-14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs marcados com um * utilizar tecnologia de Intel® Turbo para aumentar a frequência de CPU e forneça uma intensidade do desempenho.  A quantidade da intensidade pode variar consoante o tamanho da memória virtual, carga de trabalho e outros das cargas de trabalho em execução no anfitrião do mesmo.

## <a name="size-tables"></a>Tabelas de tamanho

As tabelas seguintes mostram os tamanhos e as capacidades fornecem.

* Capacidade de armazenamento é apresentada em unidades da Chaveta ou 1024 ^ 3 bytes. Quando comparar discos medida em GB (1000 ^ 3 bytes) para discos medidos em da Chaveta (1024 ^ 3) não se esqueça de que os números de capacidade tendo em conta da Chaveta podem aparecer mais pequenos. Por exemplo, 1023 beça = 1098.4 GB

* Débito do disco é medido em operações de entrada/saída por segundo (IOPS) e MBps onde MBps = 10 ^ 6 bytes/seg.

* Discos de dados podem funcionar em modos colocados em cache ou em cache. Para a operação de disco de dados em cache, o modo de cache de anfitrião está definido para **só de leitura** ou **ReadWrite**.  Para a operação de disco de dados colocados em cache, o modo de cache anfitrião está definido para **nenhum**.

* Largura de banda de rede máxima é a agregado largura de banda máxima atribuídos e atribuídas por tipo VM. A largura de banda máxima fornece orientações para selecionar o tipo VM direita para garantir a capacidade de rede suficiente está disponível. Quando mover entre baixa, definições de impressão, como sendo de alta e muito alta, o débito aumentará em conformidade. O desempenho da rede real irá depender de vários fatores incluindo rede e cargas de aplicação e as definições de rede de aplicação.


## <a name="a-series"></a>Uma série

| Tamanho        | Núcleos CPU | Memória: beça | DISCO local: beça | Discos de dados de máximo | Débito do disco máximo dados: IOPS | NIC de máximo / largura de banda de rede |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / baixa                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / moderado              |
| Standard_A2 | 2         | 3,5 GB       | 135                   | 4              | 4 x 500              | 1 / moderado              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / alta                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / alta                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1 / moderado              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / alta                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / alta                  |

## <a name="a-series---compute-intensive-instances"></a>Série - com muitos cluster instâncias

Para informações e considerações sobre como utilizar estes tamanhos, consulte o artigo [sobre VMs de série de uma série de H e cluster-a com um grau elevado](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Tamanho         | Núcleos CPU | Memória: beça | DISCO local: beça | Discos de dados de máximo | Débito do disco máximo dados: IOPS | NIC de máximo / largura de banda de rede |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alta                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muito alta             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / alta                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / muito alta             |

* RDMA capaz

## <a name="d-series"></a>Série de D


| Tamanho         | Núcleos CPU | Memória: beça | Local SSD: beça | Discos de dados de máximo | Débito do disco máximo dados: IOPS | NIC de máximo / largura de banda de rede |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / muito alta             |

## <a name="dv2-series"></a>Série de Dv2

| Tamanho            | Núcleos CPU | Memória: beça | Local SSD: beça | Discos de dados de máximo | Débito do disco máximo dados: IOPS | NIC de máximo / largura de banda de rede |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderado              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8 / extremamente elevado        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / alta                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / alta                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / alta                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / extremamente elevado        |
| Standard_D15_v2 | 20        | 140          | 1.000                | 40             | 40 x 500             | 8 / extremamente elevado        |

## <a name="g-series"></a>Série de G

| Tamanho        | Núcleos CPU | Memória: beça  | Local SSD: beça  | Discos de dados de máximo | Débito do disco máximo: IOPS | NIC de máximo / largura de banda de rede |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / alta                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / alta                  |
| Standard_G3 | 8         | 112          | 1,536                | 16             | 16 x 500           | 4 / muito alta             |
| Standard_G4 | 16        | 224          | 3.072                | 32             | 32 x 500           | 8 / extremamente elevado        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8 / extremamente elevado        |


## <a name="h-series"></a>Série de H

Azure H série virtual máquinas são o próximo geração alto desempenho computação que VMS destinadas necessidades utilizaria fim alta, como molecular modelação e utilizaria dynamics de fluidos. Estes 8 e 16 core VMs foram criadas na tecnologia de processador Intel 2667 Haswell E5 V3 DDR4 memória e armazenamento SSD com base no local. 

Para além de energia de CPU substancial, a série de H oferece diversificadas opções para à rede de RDMA de latência baixa utilizando fosfatos InfiniBand e várias configurações de memória para suportar requisitos utilizaria intenso de memória.


| Tamanho           | Núcleos CPU | Memória: beça | Local SSD: beça | Discos de dados de máximo | Débito do disco máximo: IOPS | NIC de máximo / largura de banda de rede |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / alta                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muito alta                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / alta                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muito alta                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / muito alta                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / muito alta                  |


* RDMA capaz

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Notas: Padrão A0 - A4 com o clip e PowerShell 

No modelo clássico de implementação, alguns nomes de tamanho VM são um pouco diferentes no clip e PowerShell:

* Standard_A0 é ExtraSmall 
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Configurar os tamanhos para serviços em nuvem

Pode especificar o tamanho de Máquina Virtual de uma instância de função como parte do modelo de serviço descrito do [ficheiro de definição de serviço](cloud-services-model-and-package.md#csdef). O tamanho da função determina o número de núcleos CPU, a capacidade de memória e o tamanho de sistema do ficheiro local que é atribuído a uma instância em execução. Escolha um tamanho de papel com base nas necessidades de recursos da sua aplicação.

Eis um exemplo para definir o tamanho do papel para ser [Standard_D2](#general-purpose-d) para uma instância de função Web:

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a [subscrição do azure e limites de serviço, quotas e as restrições](../azure-subscription-service-limits.md).
- Saiba mais [sobre VMs de série de uma série de H e com muitos cluster](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) das cargas de trabalho como alto desempenho computação HPC ().

