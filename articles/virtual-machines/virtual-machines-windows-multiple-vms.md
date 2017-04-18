<properties
    pageTitle="Criar várias máquinas virtuais | Microsoft Azure"
    description="Opções para criar várias máquinas virtuais no Windows"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="guybo"/>

# <a name="create-multiple-azure-virtual-machines"></a>Criar múltiplas máquinas virtuais Azure

Existem muitos cenários onde precisa para criar um grande número de semelhantes máquinas virtuais (VMs). Alguns exemplos incluem computação de alto desempenho (HPC), análise de dados em grande escala, muitas vezes sem estado e dimensionáveis camada ou back-end servidores (tais como webservers) e distribuídas bases de dados.

Este artigo aborda as opções disponíveis para criar múltiplas VMs no Azure. Estas opções tirar partido do simples casos onde pode cria manualmente uma série de VMs. Para criar VMs muitos, os processos que normalmente utilizar não dimensionar bem se de que precisa para criar mais do que alguns VMs.

Uma forma de criar muitos VMs semelhantes é utilizar a construção de Gestor de recursos do Azure de _ciclos de recursos_.

## <a name="resource-loops"></a>Ciclos de recursos

Recurso ciclos são abreviatura sintácticas no Gestor de recursos do Azure modelos. Ciclos de recursos podem criar um conjunto de recursos do mesmo modo configurados reproduzida continuamente. Pode utilizar ciclos de recursos para criar várias contas de armazenamento, interfaces de rede ou máquinas virtuais. Para mais informações sobre ciclos de recursos, consulte [Criar VMs na disponibilidade define utilizando ciclos de recursos](https://azure.microsoft.com/documentation/templates/201-vm-copy-index-loops/).

## <a name="challenges-of-scale"></a>Desafios de escala

Apesar de recurso ciclos facilitar a construir uma infraestrutura da nuvem em escala e produzir mais concisos modelos, permanecem determinados desafios. Por exemplo, se utilizar um ciclo de recursos para criar 100 máquinas virtuais, terá de controladores de interface de rede (NIC) se ligam às contas de armazenamento e VMs correspondentes. Uma vez que o número de VMs é provável que seja diferente do número de contas de armazenamento, terá de lidar com tamanhos de ciclo de recurso diferente, demasiado. Estes são os problemas solvable, mas a complexidade significativamente aumenta com escala.

Outro HIP ocorre quando precisar de uma infraestrutura que se adapta elastically. Por exemplo, poderá querer uma infraestrutura autoscale que automaticamente aumenta ou diminui o número de VMs em resposta a carga de trabalho. VMs não fornecem um mecanismos integrado para variar número (escala de saída e a escala no). Se dimensionar no removendo VMs, é difícil garantir a disponibilidade de alta, certificando-se de que são balanceamento VMs através de atualização e falhas domínios.

Por fim, quando utiliza um ciclo de recursos, várias chamadas para criar recursos aceda ao hardware subjacente. Quando várias chamadas criar recursos semelhantes, Azure tem uma oportunidade implícita para melhorar a relativamente a este design e otimizar implementação fiabilidade e desempenho. Este é onde _conjuntos de escala de máquina virtual_ está disponível.

## <a name="virtual-machine-scale-sets"></a>Conjuntos de escala de máquina virtual

Os conjuntos de escala de máquina virtual são um recurso Azure calcular para implementar e gerir um conjunto de VMs idênticos. Com todos os VMs configurado a mesma, escala VM conjuntos forem dimensionar no e dimensionar saída. Basta alterar o número de VMs no conjunto. Também pode configurar os conjuntos de escala VM para autoscale com base nos pedidos da carga de trabalho.

Para aplicações de que necessita para dimensionar recursos de cluster saída e na, operações de escala implicitamente são distribuídas por falhas e actualização de domínios.

Em vez de correlacionar vários recursos como NIC e VMs, um conjunto de escala VM tem de rede, armazenamento, máquina virtual e propriedades de extensão que pode configurar centralmente.

Para obter uma introdução aos conjuntos de escala VM, consulte a [escala de Máquina Virtual define a página de produto](https://azure.microsoft.com/services/virtual-machine-scale-sets/). Para informações mais detalhadas, consulte a [documentação de conjuntos de escala máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/).
