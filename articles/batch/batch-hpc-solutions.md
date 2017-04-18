<properties
   pageTitle="Soluções de lote e HPC na nuvem | Microsoft Azure"
   description="Saiba mais sobre o lote e cenários (HPC e calcular grande) de computação de alto desempenho e opções de solução no Azure"
   services="batch, virtual-machines, cloud-services"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-compute"
   ms.date="07/27/2016"
   ms.author="danlep"/>

# <a name="batch-and-hpc-solutions-in-the-azure-cloud"></a>Soluções de lote e HPC na nuvem Azure

Azure oferece soluções de nuvem eficientes, dimensionáveis para o lote e a computação de alto desempenho (HPC) - também denominadas *Calcular grande*. Saiba mais aqui sobre calcular grande das cargas de trabalho e serviços do Azure para suportá-los ou ir diretamente para [cenários de solução](#scenarios) neste artigo. Este artigo destina-se principalmente responsáveis técnicos, os gestores de TI e fornecedores independentes de software, mas outras os programadores e profissionais de TI podem utilizá-la para se familiarizar com estas soluções.

As organizações têm problemas de informáticos em grande escala: engenharia estrutura e análise, composição de imagem, modelação complexa, simulações Monte Carlo, cálculos financeiros risco e outras pessoas. Azure ajuda as organizações resolver estes problemas com os recursos, escala e agenda que necessitam. Com o Azure, organizações podem:

* Criar soluções híbrido, expandir um cluster HPC no local para descarregar das cargas de trabalho de pico na nuvem

* Executar ferramentas de cluster HPC e das cargas de trabalho completamente no Azure

* Utilizar os serviços Azure geridos e dimensionáveis como [lote](https://azure.microsoft.com/documentation/services/batch/) para executar com muitos cluster cargas de trabalho sem ter de implementar e gerir a infraestrutura de cluster

Apesar de para além do âmbito deste artigo, o Azure também fornece os programadores e parceiros de um conjunto completo de funcionalidades, escolhas de arquitectura e ferramentas de desenvolvimento para construir em grande escala, personalizados calcular grande fluxos de trabalho. E uma ecossistema parceiro crescente está pronta para o ajudar a tornar o seu cargas de trabalho calcular grande produtivo na nuvem Azure.


## <a name="batch-and-hpc-applications"></a>Aplicações do lote e HPC

Ao contrário web aplicações e muitas aplicações de linha de negócio, lote e HPC aplicações tem um início definido e um fim e podem executar uma agenda ou a pedido, por vezes para horas ou já. A maior parte abrange duas categorias principais: *condições paralelas* (por vezes denominada "embarrassingly paralelas", porque os problemas que resolver conduzem a executados em paralelo em vários computadores ou processadores) e *intimamente à forma*. Consulte a tabela seguinte para obter mais informações sobre estes tipos de aplicação. Algumas solução Azure abordagens funcionem melhor para um tipo ou a outra.

>[AZURE.NOTE] Na secção e HPC soluções, uma instância em execução de uma aplicação é normalmente denominada uma *tarefa*e cada tarefa poderá obter dividida em *tarefas*. E os recursos de cluster agrupadas para a aplicação são denominados frequentemente *Calcular nós*.

Tipo | Características | Exemplos
------------- | ----------- | ---------------
**Condições paralelas**<br/><br/>![Condições paralelas][parallel] |• Computadores individuais executadas de forma independente lógica da aplicação<br/><br/> • Adicionar computadores permite que a aplicação dimensionar e reduzir o tempo de cálculo<br/><br/>• Aplicação consiste em executáveis separada, ou estiver dividido num grupo de serviços chamado por um cliente (uma arquitetura de orientados para serviços ou SOA, aplicação) |• Modelação de riscos financeiros<br/><br/>• Composição de imagem e processamento de imagens<br/><br/>• Codificação de multimédia e transcodificação<br/><br/>• Monte Carlo simulações<br/><br/>• Testes de software
**Intimamente à forma**<br/><br/>![Intimamente à forma][coupled] |• Aplicação requer nós de cluster para interagir ou exchange resultados intermédios<br/><br/>• Cluster nós podem comunicar com a mensagem prisma Interface (MPI), um protocolo de comunicações comum para computação paralelas de<br/><br/>• A aplicação é sensível a latência da rede e a largura de banda<br/><br/>• Desempenho da aplicação pode ser melhorado ao utilizar tecnologias de rede de alta velocidade, tais como InfiniBand e remoto acesso directo à memória (RDMA) |• Adamastor e gás modelação depósito<br/><br/>Estrutura de engenharia • e a análise, tal como utilizaria fluidos dynamics<br/><br/>• Simulações física como causa uma falha de carro e reacções nucleares<br/><br/>• Meteorologia previsão

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>Considerações para executar o lote e aplicações de HPC na nuvem

Pode migrar facilmente muitas aplicações que foram concebidas para executar no clusters de HPC no local para Azure ou para um ambiente híbrido (cruzada local). No entanto, podem existir algumas limitações ou considerações, incluindo:


* **Disponibilidade de recursos na nuvem** – dependendo do tipo de recursos de cluster de nuvem que utilizar, poderá não conseguir depender de disponibilidade de máquina contínua durante a execução de uma tarefa. Processamento de estado e progresso verificam apontar está a comuns técnicas para processar falhas breves possíveis e mais necessárias ao utilizar recursos na nuvem.


* **Acesso a dados** - técnicas de acesso de dados com frequência disponíveis em clusters de empresa, tais como NFS, poderão necessitar de configuração especial na nuvem. Em alternativa, poderá ter de tomar práticas de acesso de dados diferentes e padrões para a nuvem.

* **Movimentação de dados** - para as aplicações que processam grandes quantidades de dados, estratégias são necessários para mover os dados para armazenamento em nuvem e para calcular recursos. Poderá ter alta velocidade entre instalações como [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)de rede. Considere também legal, reguladora, ou limitações de política para armazenar ou aceder a esses dados.


* **Licenciamento** - verificação com o fornecedor de qualquer aplicação comercial para obter as restrições de licenciamento ou outras para executar na nuvem. Nem todos os fornecedores oferecem repartição licenciamento. Poderá ter de o plano para um servidor de licenciamento na nuvem para a sua solução ou ligar a um servidor de licença no local.


### <a name="big-compute-or-big-data"></a>Cluster grande ou dados grandes?

Da linha divisória entre aplicações calcular grande e Big Data nem sempre é limpar e algumas aplicações podem ter características de ambos. Ambas envolvem executar cálculos em grande escala, normalmente no clusters de computadores. Mas podem ser a solução abordagens e as ferramentas de suporte.

• **Calcular grande** tenda a envolvam aplicações que dependam CPU power e memória, tais como simulações engenharia, riscos financeiros modelação e composição digital. A infraestrutura para obter uma solução calcular grande pode incluir computadores com processadores multicore especializados para efetuar cálculo bruto e especializado, alta velocidade hardware de rede para ligar os computadores.

• **Big Data** resolva os problemas de análise de dados que envolvam grandes quantidades de dados que não podem ser geridos por um único sistema de gestão do computador ou base de dados. Alguns exemplos incluem grandes volumes de registos web ou outros dados de business intelligence. Dados grandes tendem a depender mais capacidade do disco e um desempenho e/s que no CPU power. Também existem ferramentas de dados grande especializadas como Apache Hadoop para gerir as cluster e partição os dados. (Para obter informações sobre outras soluções de Azure Hadoop e Azure HDInsight, consulte [Hadoop](https://azure.microsoft.com/solutions/hadoop/)).

## <a name="compute-management-and-job-scheduling"></a>Gestão de cluster e o agendamento da tarefa

Executar o lote e HPC aplicações frequentemente inclui um *Gestor de clusters* e um *Programador de tarefas* para ajudar a gerir os recursos de cluster agrupadas e atribuí-las para as aplicações que são executadas as tarefas. Estas funções podem ser executadas por ferramentas separadas, ou uma ferramenta integrada ou serviço.

* **Gestor de clusters** - disposições, liberta e instituição calcular recursos (ou calcular nós). Gestor de cluster pode automatizar instalação de imagens do sistema operativo e aplicações em nós de cluster, dimensionar recursos de cluster de acordo com pedidos e monitorizar o desempenho de nós.

* **Programador** - Especifica os recursos (como processadores ou memória) uma aplicação necessidades e as condições quando é executado. Um programador mantém uma fila de tarefas e atribui-lhes com base numa prioridade atribuída ou outras características recursos.

Clusters e ferramentas para baseados no Windows e baseado em Linux clusters de agendamento de projeto podem migrar bem para Azure. Por exemplo, para o [Microsoft aos Pack](https://technet.microsoft.com/library/cc514029), solução de cluster de cluster gratuito da Microsoft para Windows e Linux HPC cargas de trabalho, oferece várias opções para executar no Azure. Também pode criar Linux clusters para executar ferramentas Abrir origem como binário e SLURM. Também pode trazer consigo soluções de grelha comercial para Azure, tais como [TIBCO DataSynapse GridServer](http://www.tibco.com/company/news/releases/2016/tibco-to-accelerate-cloud-adoption-of-banking-and-capital-markets-customers-via-microsoft-collaboration), [IBM plataforma Symphony](http://www-01.ibm.com/support/docview.wss?uid=isg3T1023592)e [Univa motor de grelha](http://www.univa.com/products/grid-engine).

Como é mostrado nas secções seguintes, também pode tirar partido dos serviços Azure para gerir os recursos de cluster e agendar ferramentas de gestão de cluster tradicional tarefas sem (ou, para além).


## <a name="scenarios"></a>Cenários

Seguem-se três cenários comuns para executar a calcular grande das cargas de trabalho no Azure utilizando soluções de cluster HPC existentes, Azure serviços ou uma combinação das duas. Considerações chaves para escolher cada cenário estão listadas, mas não estão exaustivas. Mais sobre os serviços disponíveis Azure, que poderá utilizar na sua solução são posteriores no artigo.

  | Cenário | Porquê escolhê-lo?
------------- | ----------- | ---------------
**Rajada um cluster HPC para Azure**<br/><br/>[! [Cluster rajada] [burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> Saiba mais:<br/>• [Rajada instâncias do Azure trabalhador com aos Pack](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [Configurar híbrida calcular cluster com aos Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [Rajada lote Azure com HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/>|• Combinar o [Pacote do Microsoft aos](https://technet.microsoft.com/library/cc514029) ou outros cluster no local com recursos adicionais Azure numa solução híbrido.<br/><br/>• Alargar as cargas de trabalho calcular grande para ser executada numa plataforma como um serviço (PaaS) instâncias de máquina virtual (atualmente apenas Windows Server).<br/><br/>• Aceder a um arquivo de dados ou servidor de licenças no local através de uma rede virtual Azure opcional|• Tem um cluster HPC existente e precisa de mais recursos <br/><br/>• Que não pretende comprar e gerir a infraestrutura adicional do cluster HPC<br/><br/>• Tiver breves períodos de pedido de pico ou projetos especiais
**Criar um cluster HPC completamente no Azure**<br/><br/>[! [Cluster no IaaS] [iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>Saiba mais:<br/>• [Soluções de cluster HPC no Azure](./big-compute-resources.md)<br/><br/>|• Rapidamente forma consistente implementar e as aplicações e ferramentas de cluster no personalizados ou padrão infraestrutura Windows ou Linux como um máquinas virtuais do serviço (IaaS).<br/><br/>• Executar vários calcular grande das cargas de trabalho utilizando a tarefa de agendamento de solução da sua escolha.<br/><br/>• Utilizar serviços Azure adicionais, incluindo redes e armazenamento para criar soluções completas baseado na nuvem. |• Que não pretende comprar e gerir a infraestrutura adicional do cluster Linux ou do Windows<br/><br/>• Tiver breves períodos de pedido de pico ou projetos especiais<br/><br/>• Tem de ter uma adicional cluster para uma altura, mas não quer investir em computadores e espaço para implementá-lo<br/><br/>• Pretender descarregar a aplicação com muitos cluster para que é executado como um serviço totalmente na nuvem
**Dimensionar saída de uma aplicação em paralelo para Azure**<br/><br/>[! [Azure Batch] [batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>Saiba mais:<br/>• [Noções básicas do lote Azure](./batch-technical-overview.md)<br/><br/>• [Começar a trabalhar com a biblioteca do Azure lote para .NET](./batch-dotnet-get-started.md)|• Desenvolver com o [Azure lote](https://azure.microsoft.com/documentation/services/batch/) para dimensionar saída de vários calcular grande das cargas de trabalho para ser executado no conjuntos de dados do Windows ou Linux máquinas virtuais.<br/><br/>• Utilizar um serviço da plataforma Azure para gerir a implementação e autoscaling de máquinas virtuais, agendamento da tarefa, recuperação de falhas, movimento de dados, gestão de dependência e implementação da aplicação.|• Não pretende gerir calcular os recursos ou um programador; em alternativa, pretende focar-se em execução das aplicações<br/><br/>• Pretender descarregar a aplicação com muitos cluster para que é executado como um serviço na nuvem<br/><br/>• Que quer Dimensionar automaticamente os recursos de cluster para corresponder a carga de trabalho de cluster


## <a name="azure-services-for-big-compute"></a>Azure serviços para calcular grande

Eis mais informações sobre o cluster, dados, redes e serviços relacionados que pode combinar para calcular grande soluções e fluxos de trabalho. Para obter orientação detalhada sobre os serviços Azure, consulte a [documentação](https://azure.microsoft.com/documentation/)de serviços Azure. Os [cenários](#scenarios) neste artigo Mostrar apenas algumas formas de utilizar estes serviços.

>[AZURE.NOTE] Azure regularmente apresenta novos serviços que possam ser úteis para o seu cenário. Se tiver dúvidas, contacte um [parceiro Azure](https://pinpoint.microsoft.com/en-US/search?keyword=azure) ou e-mail *bigcompute@microsoft.com*.

### <a name="compute-services"></a>Calcular serviços

Serviços de cluster Azure são o essencial de uma solução calcular grande e as vantagens de oferta de serviços diferente cluster para cenários diferentes. A um nível básico, estes serviços oferecem modos diferentes para as aplicações ser executado em instâncias com base em máquina virtual cluster que Azure fornece com a tecnologia de servidor de Windows Hyper-V. É possível executar estes instâncias padrão e personalizadas ferramentas e os sistemas operativos Linux e Windows. Azure dá-lhe uma escolha de [tamanhos de instância](../virtual-machines/virtual-machines-windows-sizes.md) com configurações diferentes de CPU núcleos, memória, capacidade do disco e outras características. Consoante as suas necessidades, pode dimensionar as instâncias a milhares de núcleos e, em seguida, dimensionar para baixo, quando precisar de menos recursos.

>[AZURE.NOTE] Tirar partido das instâncias com muitos cluster Azure para melhorar o desempenho e escalabilidade das cargas de trabalho HPC incluindo paralelas MPI as aplicações que requerem uma latência baixa e de rede de aplicação de débito alta. Consulte [Acerca da série de H e com muitos cluster VMs A série](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).  

Serviço | Descrição
------------- | -----------
**[Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Fornece infraestruturas de calcular como um serviço (IaaS) com a tecnologia Microsoft Hyper-V<br/><br/>• Permitem-lhe flexível aprovisionar e gerir computadores persistente nuvem a partir do imagens Windows Server ou Linux padrão do [Azure Marketplace](https://azure.microsoft.com/marketplace/), ou imagens e discos de dados que forneceu<br/><br/>• Podem ser implementada e geridos como [Conjuntos de escala VM](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) para construir services em grande escala da máquinas virtuais idênticas, com autoscaling para aumentar ou diminuir capacidade automaticamente<br/><br/>• Executar no local calcular cluster ferramentas e aplicações totalmente na nuvem<br/><br/>
**[Serviços em nuvem](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• Pode executar calcular grande aplicações no trabalho instâncias de função, que são máquinas virtuais a executar uma versão do Windows Server e são geridas totalmente por Azure<br/><br/>• Permitem que as aplicações de dimensionáveis, fiáveis com baixa overhead administrativo, a ser executada numa plataforma como um modelo de serviço (PaaS)<br/><br/>• Precisem de ferramentas adicionais ou desenvolvimento para integrar com soluções de cluster HPC no local
**[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• Em grande escala paralelo e lote das cargas de trabalho é executado num serviço totalmente gerido<br/><br/>• Fornece agendamento da tarefa e autoscaling de um conjunto de máquinas virtuais gerido<br/><br/>• Permite que os programadores criar e executar as aplicações como um serviço ou na nuvem-activar aplicações existentes<br/>

### <a name="storage-services"></a>Serviços de armazenamento

Normalmente, uma solução calcular grande funciona num conjunto de dados de entrada e gera dados para os seus resultados. Alguns dos serviços de armazenamento Azure utilizados em grande calcular soluções incluem:

* [Blob, tabela e armazenamento de filas](https://azure.microsoft.com/documentation/services/storage/) - gerir grandes quantidades de dados não estruturados, dados de NoSQL e mensagens para o fluxo de trabalho e de comunicação, respetivamente. Por exemplo, poderá utilizar o armazenamento de BLOBs para grandes conjuntos de dados técnicos ou para as imagens de entrada ou os processos de aplicação de ficheiros de multimédia. Poderá utilizar filas para comunicação assíncrona numa solução. Consulte o artigo [Introdução ao Microsoft Azure armazenamento](../storage/storage-introduction.md).

* [Armazenamento de ficheiros do azure](https://azure.microsoft.com/services/storage/files/) - partilha de ficheiros e dados no Azure utilizando o protocolo SMB padrão, que é necessário para algumas soluções de cluster HPC comuns.

* [Arquivo de dados de Lake](https://azure.microsoft.com/services/data-lake-store/) - fornece um sistema de ficheiros distribuído do Apache Hadoop hyperscale para a nuvem, útil para lote, em tempo real e a análise de interativa.

### <a name="data-and-analysis-services"></a>Serviços de dados e análise

Alguns cenários de calcular grande envolvam fluxos de dados em grande escala ou geram os dados que necessita de processamento suplementar ou análise. Azure oferece vários serviços de dados e análise, incluindo:

* [Dados fábrica](https://azure.microsoft.com/documentation/services/data-factory/) - compilações condicionados por dados fluxos de trabalho (tubagens) esse participar, Agregar e dados de transformação de no local, baseada na nuvem e arquivos de dados da Internet.

* [Base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/) - fornece principais funcionalidades de um sistema de gestão de base de dados relacional do Microsoft SQL Server num serviço gerido.

* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - ser implementada e disposições Windows Server ou baseado em Linux Hadoop Apache clusters na nuvem para gerir, analisar e um relatório nos dados grandes.

* [Formação de máquina](https://azure.microsoft.com/documentation/services/machine-learning/) - ajuda-o a criar, teste, operar e gerir soluções analíticas aspeto do Office num serviço totalmente gerida.

### <a name="additional-services"></a>Serviços adicionais

A solução calcular grande poderá necessitar de outros serviços Azure para ligar a recursos no local ou em outros ambientes. Alguns exemplos incluem:

* [Rede virtual](https://azure.microsoft.com/documentation/services/virtual-network/) - cria uma secção logicamente isolada no Azure para ligar recursos Azure umas às outras ou para o seu centro de dados no local. Com uma rede virtual cruzada local, calcular grande aplicações podem aceder ao dados no local, serviços do Active Directory e servidores de licenças

* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - cria uma ligação privada entre os centros de dados do Microsoft e infraestrutura de que está no local ou num ambiente localização cocriação. ExpressRoute fornece uma maior segurança, mais fiabilidade, velocidades mais rápidas e inferiores latências que as ligações típicas através da Internet.

* [Serviço Bus](https://azure.microsoft.com/documentation/services/service-bus/) - fornece mecanismos diversos para aplicações comunicar ou intercâmbio de dados, se estiverem localizados Azure, numa outra plataforma na nuvem, ou num centro de dados.

## <a name="next-steps"></a>Próximos passos

* Consulte o artigo [Recursos técnicos para o lote e HPC](big-compute-resources.md) para localizar a orientação técnica para criar a sua solução.

* Abordam as suas opções de Azure com parceiros incluindo ciclo computação e UberCloud.

* Leia sobre soluções Azure grande calcular emitidas por [Colunas Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222), [Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/), [ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)e [d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088).

* Para os anúncios mais recentes, consulte o [Microsoft aos e lote blogue da equipa](http://blogs.technet.com/b/windowshpc/) e o [Azure blogue](https://azure.microsoft.com/blog/tag/hpc/).

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png
