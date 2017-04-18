<properties
    pageTitle="Armazenamento de Azure Premium: Estruturar para obter um desempenho | Microsoft Azure"
    description="Estruturar aplicações de alto desempenho utilizando o armazenamento do Windows Azure Premium. Armazenamento de Premium oferece suporte de alto desempenho, baixa latência disco para posso/O-com um grau elevado das cargas de trabalho em execução no máquinas virtuais do Azure."
    services="storage"
    documentationCenter="na"
    authors="aungoo-msft"
    manager="tadb"
    editor="tysonn" />

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="aungoo"/>

# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento do Azure Premium: Design de alto desempenho

## <a name="overview"></a>Descrição geral  
Este artigo fornece diretrizes para criar aplicações de alto desempenho utilizando o armazenamento do Azure Premium. Pode utilizar as instruções fornecidas neste documento combinado com desempenho melhores práticas aplicáveis às tecnologias utilizadas pela sua aplicação. Para ilustrar as diretrizes, utilizámos do SQL Server em execução no Premium armazenamento como exemplo ao longo deste documento.

Enquanto podemos abordam cenários de desempenho para a camada de armazenamento neste artigo, terá de otimizar a camada de aplicação. Por exemplo, se estiver a alojar num Farm do SharePoint no armazenamento do Windows Azure Premium, pode utilizar os exemplos do SQL Server a partir deste artigo para optimizar o servidor de base de dados. Para além disso, otimizar o servidor Web e Application server para obter o desempenho a maior parte dos Farm do SharePoint.

Este artigo irá ajudá-respostas a questões recorrentes sobre otimizar o desempenho da aplicação no Azure Premium armazenamento, a seguir

-   Como medir o desempenho da aplicação?  
-   Porque são não ver esperado alto desempenho?  
-   Quais os factores influenciam o desempenho da aplicação Premium armazenamento?  
-   Como é que estes factores influenciar desempenho da sua aplicação Premium armazenamento?  
-   Como pode otimizar para IOPS, largura de banda e latência?  

Fornecemos estas diretrizes especificamente para Premium armazenamento porque das cargas de trabalho em execução no armazenamento Premium altamente são sensíveis a maiúsculas e de desempenho. Fornecemos exemplos adequado. Também pode aplicar algumas destas diretrizes para aplicações em execução no IaaS VMs com discos de armazenamento padrão.

Antes de começar, se estiver familiarizado com o armazenamento de Premium, leia primeiro o [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](storage-premium-storage.md) artigo e [escalabilidade de armazenamento do Azure Premium e o desempenho destinos](storage-scalability-targets.md#premium-storage-accounts).

## <a name="application-performance-indicators"></a>Indicadores de desempenho de aplicação  
Vamos avaliar se está a executar uma aplicação bem ou não a utilizar como indicadores de desempenho, como a mesma rapidez com uma aplicação está a processar um pedido de utilizador, quantidade de dados está a processar por pedido de uma aplicação, quantas solicitações é uma transformação de aplicação num determinado período de tempo, quanto tempo que um utilizador tem de esperar até obter uma resposta depois de submeter o seu pedido. Os termos técnicos destes indicadores de desempenho são IOPS, débito ou largura de banda e latência.

Nesta secção, discutimos os indicadores de desempenho comuns no contexto de armazenamento de Premium. Na secção seguinte, recolher requisitos da aplicação, vai aprender medir destes indicadores de desempenho para a sua aplicação. Mais tarde otimizar o desempenho de aplicações, irá aprender sobre os fatores que afetam os destes indicadores de desempenho e recomendações para otimizar o-los.

## <a name="iops"></a>IOPS  
IOPS é o número de pedidos de que a aplicação está a enviar para os discos de armazenamento num segundo. Uma operação de entrada/saída pode ser lido ou escrever, sequenciais ou aleatório. Aplicações de OLTP, como um Web site online de revenda necessitam processar imediatamente muitos pedidos de utilizador em simultâneo. Os pedidos de utilizador são inserir e atualizar as transações de base de dados intenso, que a aplicação deve ser processado rapidamente. Por conseguinte, aplicações de OLTP requerem IOPS muito alta. Estas aplicações alça milhões de pedidos de IO pequenos e aleatórios. Se tiver uma aplicação dessa, tem de estruturar a infraestrutura de aplicação para otimizar para IOPS. Na secção posterior, *Otimizar o desempenho de aplicações*, discutimos detalhadamente os fatores que tem de considerar para obter IOPS alta.

Quando anexa um disco de armazenamento premium a sua escala alta VM, disposições Azure para si um número garantido de IOPS de acordo com a especificação de disco. Por exemplo, um disco P30 Aprovisiona 5000 IOPS. Cada escala alta tamanho da memória virtual também tem um limite IOPS específico que pode suportar. Por exemplo, uma VM GS5 padrão tem 80.000 IOPS limitar.

## <a name="throughput"></a>Débito  
Largura de banda ou débito é a quantidade de dados que a aplicação está a enviar para os discos de armazenamento no intervalo de tempo especificado. Se a sua aplicação está a executar operações de entrada/saída com tamanhos de unidade IO grandes, necessita de débito alto. Aplicações de armazém de dados tendem para pesquisa de operações intenso que acederem grandes partes de dados de cada vez e frequentemente efectuam operações em massa de problema. Por outras palavras, essas aplicações requerem débito superior. Se tiver uma aplicação dessa, é necessário estruturar a sua infraestrutura de otimizar para débito. Na secção seguinte, discutimos detalhadamente os fatores deve otimizar para realizar esta.

Quando anexa um disco de armazenamento premium a uma escala de alta VM, Azure disposições débito de acordo com a especificação do disco. Por exemplo, um disco P30 Aprovisiona 200 MB por segundo disco débito. Cada escala alta tamanho da memória virtual também tem como limite débito específico que pode suportar. Por exemplo, padrão GS5 VM tem um débito máximo de 2000 MB por segundo.

Existe uma relação entre débito e IOPS conforme mostrado na fórmula abaixo.

![](media/storage-premium-storage-performance/image1.png)

Por isso, é importante determinar os valores de débito e IOPS ideais que requer a aplicação. Como tentar otimizar um, o outro também é afetado. Numa secção posterior, *Otimizar o desempenho de aplicações*, abordaremos mais detalhes sobre como optimizar IOPS e débito.

## <a name="latency"></a>Latência  
A latência é o tempo que demora uma aplicação para receber um único pedido, enviá-la para discos de armazenamento e enviar a resposta para o cliente. Esta é uma medida crítica de desempenho de uma aplicação para além de IOPS e débito. A latência de um disco de armazenamento premium é o tempo que demora a obter as informações para um pedido e comunicá-lo a voltar a sua aplicação. Armazenamento de Premium fornece latências baixos consistentes. Se ativar anfitrião só de leitura em cache no discos de armazenamento premium, pode obter muito latência leitura inferior. Abordaremos cache de disco mais detalhadamente na secção posterior no *Otimizar o desempenho de aplicações*.

Quando estiver a optimizar a aplicação para obter mais elevados IOPS e débito, irão afetar a latência da sua aplicação. Depois de otimização do desempenho da aplicação, sempre avalie a latência da aplicação para evitar comportamentos inesperados latência alta.

## <a name="gather-application-performance-requirements"></a>Reunir requisitos de desempenho de aplicação  
Aplicações de alto desempenho em execução no Azure Premium armazenamento de estruturar o primeiro passo é para compreender os requisitos de desempenho da sua aplicação. Após recolher requisitos de desempenho, pode otimizar a sua aplicação para alcançar o desempenho ideal mais.

Na secção anterior, podemos explicado os indicadores de desempenho comuns, IOPS, débito e latência. Tem de identificar que um destes indicadores de desempenho são críticas à aplicação para entregar a experiência de utilizador pretendido. Por exemplo, IOPS alta é importante para aplicações OLTP processamento milhões de transações num segundo. Enquanto o débito alto é fundamental para as aplicações de Data Warehouse processar grandes quantidades de dados num segundo. Latência extremamente baixa é fundamental para aplicações em tempo real, como o vídeo em directo transmissão de Web sites.

Em seguida, medir os requisitos de desempenho máximo da sua aplicação em toda a sua vida útil. Utilize a lista de verificação de exemplo abaixo como um início. Grave os requisitos de desempenho máximo durante normal, períodos de carga de trabalho de pico e fora de horas. Ao identificar os requisitos para todos os níveis de cargas de trabalho, será capaz de determinar o requisito de desempenho geral da sua aplicação. Por exemplo, a carga de trabalho normal de um Web site comércio electrónico serão transações que serve durante a maior parte dos dias num ano. A carga de trabalho de pico do Web site será transações que serve durante festivos ou eventos de venda especial. A carga de trabalho de pico é normalmente experiente durante um período limitado, mas pode exigir a sua aplicação para dimensionar duas ou mais vezes respectivo funcionamento normal. Descubra o percentil 50, percentil 90 e requisitos de percentil 99. Isto ajuda a filtrar qualquer aberrantes nos requisitos de desempenho e pode concentrar-os esforços de otimizar para os valores de à direita.

**Lista de verificação de requisitos de desempenho de aplicação**

| **Requisitos de desempenho** | **50 percentil** | **Percentil 90** | **Percentil 99** |
|---|---|---|---|
| Máximo. Transações por segundo | | | |
| Operações de leitura %            | | | |
| Operações de escrita %           | | | |
| Operações aleatório %          | | | |
| Operações sequenciais %      | | | |
| Tamanho de pedido de es              | | | |
| Débito médio           | | | |
| Máximo. Débito              | | | |
| Mínimo. Latência                 | | | |
| Latência de em média              | | | |
| Máximo. CPU                     | | | |
| CPU média                  | | | |
| Máximo. Memória                  | | | |
| Memória média               | | | |
| Fila de espera profundidade                  | | | |

>**Nota importante:**  
>Deverá tomar em consideração de dimensionamento estes números com base em crescimento futuro esperado da sua aplicação. É uma boa ideia para planear para o crescimento de antecedência, porque dever-se mais difícil alterar a infraestrutura para melhorar o desempenho mais tarde.

Se tiver uma aplicação existente e pretende deslocar-se ao armazenamento de Premium, pela primeira vez a criar a lista de verificação acima para a aplicação existente. Em seguida, criar um protótipo da sua aplicação armazenamento Premium e estruturar a aplicação baseada no orientações descritas no *Desempenho da aplicação otimizar* numa secção posterior deste documento. A secção seguinte descreve as ferramentas que pode utilizar para reunir as medidas de desempenho.

Crie uma lista de verificação semelhante a sua aplicação existente para o protótipo. Utilizar ferramentas de Benchmarking pode simular as cargas de trabalho e medem o desempenho sobre a aplicação de protótipo. Consulte a secção sobre [Benchmarking](#benchmarking) para obter mais informações. Ao fazê-lo pode determinar se o armazenamento de Premium pode corresponder ou ultrapassar os seus requisitos de desempenho da aplicação. Em seguida, pode implementar as mesmas directrizes para a sua aplicação de produção.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores medir requisitos de desempenho de aplicação  
A melhor forma de medir requisitos de desempenho da sua aplicação, é utilizar a monitorização de desempenho ferramentas fornecidas pelo sistema operativo do servidor. Pode utilizar o desempenho para Windows e iostat para Linux. Estas ferramentas capturam contadores correspondente a cada medida explicada na secção acima. Tem de capturar os valores destes contadores quando a aplicação estiver em execução suas normal, pico e fora de horas das cargas de trabalho.

Contadores de monitorização do estão disponíveis para processador, memória e, cada disco lógico e disco físico do seu servidor. Quando utiliza o discos de armazenamento premium com uma VM, contadores de disco físico para cada disco de armazenamento premium, e contadores disco lógico para cada volume criado nos discos armazenamento premium. Tem de capturar os valores para os discos que alojam a sua carga de trabalho de aplicação. Se existir um mapeamento de um-para-um entre discos lógicos e físicos, pode consultar a contadores de disco físico caso contrário, consulte os contadores do disco lógicos. No Linux, o comando iostat gera um relatório de utilização da CPU e disco. O relatório de utilização do disco fornece estatísticas por dispositivo físico ou partição. Se tiver um servidor de base de dados com as respetivas dados e o registo em discos separados, recolha estes dados para ambos os discos. Abaixo de tabela descreve contadores para discos, processador e memória:

| Contador | Descrição | Desempenho | Iostat |
|---|---|---|---|
| **IOPS ou transações por segundo** | Número de pedidos e/s emitido para o disco de armazenamento por segundo. | Disco lê/seg <br> Disco escritas/seg | TP <br> r/s <br> w/s |
| **Lê disco e escritas** | % de lê e escrever operações efetuadas no disco. | % Tempo de leitura do disco <br> % De tempo de escrita de disco | r/s <br> w/s |
| **Débito** | Quantidade de dados de leitura ou escrita para o disco por segundo. | Disco Bytes lidos/seg. <br> Bytes de escrita de disco/seg | kB_read/s <br> kB_wrtn/s |
| **Latência** | Tempo total para concluir um pedido de es do disco. | Média disco sec/lidas <br> Sec/escrita no disco média | aguardar <br> svctm |
| **Tamanho de es** | O tamanho da e/s pedidos de problemas para os discos de armazenamento. | Disco média Bytes/lidas <br> Disco média Bytes/escrita | avgrq sz |
| **Fila de espera profundidade** | Número de e/s pendente pedidos em espera para serem lidos formulário ou escritas no disco de armazenamento. | Comprimento de fila de disco atual | avgqu sz |
| **Máximo. Memória** | Quantidade de memória necessária para executar a aplicação sem problemas | % Consolidada Bytes utilizado | Utilizar vmstat |
| **Máximo. CPU** | Montante CPU necessária para executar a aplicação sem problemas | Tempo de processador % | % /Util |

Saiba mais sobre [iostat](http://linuxcommand.org/man_pages/iostat1.html) e [Desempenho](https://msdn.microsoft.com/library/aa645516.aspx).


## <a name="optimizing-application-performance"></a>Otimizar o desempenho da aplicação  
Os principais fatores que influenciam desempenho de uma aplicação a ficar com armazenamento Premium são natureza de IO pedidos, tamanho da memória virtual, tamanho do disco, número de discos, cache de disco, o Multithreading e profundidade fila de espera. Pode controlar alguns dos seguintes fatores com botões fornecidas pelo sistema. A maioria das aplicações poderá não dar-lhe uma opção para alterar o tamanho de es e a fila profundidade diretamente. Por exemplo, se estiver a utilizar o SQL Server, é possível selecionar o tamanho e fila de profundidade es. SQL Server escolhe os ideais IO fila profundidade valores do tamanho e para obter o desempenho a maior parte dos. É importante compreender os efeitos de ambos os tipos de fatores sobre o desempenho da aplicação, para que pode aprovisionar o recursos adequados para satisfazer necessidades de desempenho.

Ao longo nesta secção, consulte a lista de verificação de requisitos de aplicação que criou para saber quanto precisa de otimizar o desempenho da aplicação. Com base no que, poderão determinar quais os factores desta secção terá de para o gráfico. Para assistir os efeitos de cada factor sobre o desempenho da aplicação, execute ferramentas avaliação comparativa na configuração da aplicação. Consulte a secção [Benchmarking](#Benchmarking) no final deste artigo para obter os passos executar as ferramentas de avaliação comparativa comuns no Windows e Linux VMs.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Otimizar os IOPS, débito e latência de relance  
A tabela abaixo resume todas as os fatores de desempenho e os passos para otimizar o IOPS, débito e latência. As secções a seguir este resumo serão descrevem cada factor é profundidade muito mais.

| | **IOPS** | **Débito** | **Latência** |
|---|---|---|---|
| **Cenário de exemplo** | Aplicação Enterprise OLTP que exige o muito alta transações por taxa segunda.                                                                                                                                 | Grandes quantidades da aplicação processamento de dados de armazenamento de dados da empresa. | Junto à aplicações em tempo real que requerem instantâneas respostas a pedidos de utilizador, como o jogos online. |
| Fatores de desempenho  | | | |
| **Tamanho de es** | Tamanho mais pequeno de IO rendimentos IOPS superior.                                                                                                                                                                           | Maior tamanho de es para rendimentos débito superior. | |
| **Tamanho da memória virtual** | Utilize um tamanho da memória virtual que oferece IOPS maior do que o requisito de aplicação. Consulte o artigo tamanhos VM e os respetivos limites IOPS aqui. | Utilize um tamanho da memória virtual com o limite de débito maior que o requisito de aplicação. Consulte o artigo tamanhos VM e os respetivos limites de débito aqui. | Utilize um tamanho da memória virtual que ofertas Dimensionar limites maiores que o requisito de aplicação. Consulte o artigo tamanhos VM e os respetivos limites aqui. |
| **Tamanho do disco** | Utilize um tamanho de disco que oferece IOPS maior do que o requisito de aplicação. Consulte o artigo tamanhos de discos e os respetivos limites IOPS aqui. | Utilize um tamanho de disco com o limite de débito maior que o requisito de aplicação. Consulte o artigo tamanhos de discos e os respetivos limites de débito aqui. | Utilize um tamanho de disco que ofertas Dimensionar limites maiores que o requisito de aplicação. Consulte o artigo tamanhos de discos e os respetivos limites aqui. |
| **VM e limites de escala de disco** | Limite IOPS do tamanho da memória virtual escolhido deve ser maior do que IOPS total condicionada por discos de armazenamento de premium anexados. | Limite de débito do tamanho da memória virtual escolhido deve ser maior do que débito total condicionado por discos de armazenamento de premium anexados. | Limites de escala do tamanho da memória virtual escolhido tem de ser maiores do que os limites de escala total de discos de armazenamento premium anexado. |
| **Cache de disco** | Ativar o Cache só de leitura no discos de armazenamento premium com operações frequente de leitura para obter mais elevados IOPS de leitura. | | Ativar a Cache de só de leitura no discos de armazenamento premium com operações grossas prontos para obter leitura muito baixa latências. |
| **Repartição do disco** | Utilizar vários discos e faixas-las em conjunto para obter um limite IOPS e débito superior combinado. Tenha em atenção que o combinado limite por VM deve ser mais elevado que os limites combinados de discos premium anexado. | |
| **Tamanho da faixa** | Tamanho mais pequeno de faixa para aleatório padrão IO pequenas visto no aplicações OLTP. Por exemplo, utilize o tamanho da faixa de 64KB para a aplicação do SQL Server OLTP. | Tamanho de faixa do maior para sequencial padrão IO grande visto nas aplicações do armazém de dados. Por exemplo, utilize o tamanho da faixa 256KB para a aplicação de armazém de dados do SQL Server. | |
| **Multithreading** | Utilize multithreading para transmitir número mais alto de pedidos de ao armazenamento de Premium, será direcionado para o mais elevados IOPS e débito. Por exemplo, no SQL Server configure um valor MAXDOP elevado alocar mais CPUs para o SQL Server. | |
| **Fila de espera profundidade** | Maior fila profundidade rendimentos IOPS superior. | Maior fila profundidade rendimentos débito superior. | Mais pequeno de fila de profundidade rendimentos latências inferiores. |

## <a name="nature-of-io-requests"></a>Carácter de pedidos de IO  
Um pedido de IO é uma unidade de operação de entrada/saída que vai estar a executar a aplicação. Identificar a natureza dos pedidos de IO, aleatórios ou sequenciais, leitura ou escrita, pequena ou grande, irá ajudá-lo a determinar os requisitos de desempenho da sua aplicação. É muito importante compreender a natureza dos pedidos de IO, para tornar as decisões direita ao estruturar sua infraestrutura de aplicação.

Tamanho de ES é um dos fatores mais importantes. O tamanho de ES é o tamanho do pedido de operação de entrada/saída gerado por sua aplicação. O tamanho de es tem um impacto significativo no desempenho especialmente no IOPS e largura de banda que é possível alcançar a aplicação. A fórmula seguinte mostra a relação entre IOPS, tamanho de es e largura de banda/débito.  
    ![](media/storage-premium-storage-performance/image1.png)

Algumas aplicações permitem-lhe alterar o respetivo tamanho de es, enquanto algumas aplicações não o fizer. Por exemplo, SQL Server determina o tamanho ideal do IO propriamente dito e não fornecer aos utilizadores quaisquer botões para alterá-lo. Por outro lado, Oracle fornece um parâmetro chamado [DB\_bloquear\_tamanho](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) utilizando que pode configurar o tamanho do pedido e/s da base de dados.

Se estiver a utilizar uma aplicação, que não lhe permitir alterar o tamanho de es, utilize as diretrizes neste artigo para otimizar o desempenho KPI que seja mais relevante para a sua aplicação. Por exemplo,

-   Uma aplicação OLTP gera milhões de pedidos de IO pequenos e aleatórios. Para processar estes tipo de pedidos de IO, tem de estruturar sua infraestrutura de aplicação para obter mais elevados IOPS.  
-   Uma aplicação de armazenamento de dados gera pedidos de IO grandes e sequenciais. Para processar estes tipo de pedidos de IO, tem de estruturar sua infraestrutura de aplicação para obter mais elevados largura de banda ou débito.

Se estiver a utilizar uma aplicação, que permite-lhe alterar o tamanho de es, utilize esta regra útil para o tamanho de es para além de outros diretrizes de desempenho,

-   Tamanho mais pequeno de es para obter mais elevados IOPS. Por exemplo, 8 KB para uma aplicação OLTP.  
-   Tamanho de es maior para obter mais elevado largura de banda/débito. Por exemplo, 1024 KB para uma aplicação do armazém de dados.

Eis um exemplo sobre como pode calcular a IOPS e a largura de banda/débito para a sua aplicação. Considere a hipótese de uma aplicação utilizando um disco P30. O valor máximo IOPS e débito/largura de banda um disco P30 podem alcançar é 5000 IOPS e 200 MB por segundo respetivamente. Se a sua aplicação requer o IOPS máximo do disco P30 e utilizar um tamanho mais pequeno de IO como 8 KB, a largura de banda resultante que poderá obter está agora 40 MB por segundo. No entanto, se a sua aplicação requer a débito/largura de banda máxima a partir do disco P30 e utilizar um tamanho de es maior como 1024 KB, o IOPS resultante será menor, 200 IOPS. Por conseguinte, ajustar o tamanho de es dessa-preenche os requisitos de IOPS e débito/largura de banda de ambas as sua aplicação. Tabela abaixo resume os diferentes tamanhos de IO e as respetivas correspondente IOPS e débito para um disco P30.

| **Requisito de aplicação** | **Tamanho e/s** | **IOPS** | **Largura de banda/débito** |
|-----------------------------|--------------|----------|--------------------------|
| Máximo IOPS                    | 8 KB         | 5.000    | 40 MB por segundo         |
| Máximo débito              | 1024 KB      | 200      | 200 MB por segundo        |
| Máximo débito + IOPS alta  | 64 KB        | 3.200    | 200 MB por segundo        |
| Máximo IOPS + débito alto  | 32 KB        | 5.000    | 160 MB por segundo        |

Para obter mais elevada que o valor máximo de um disco de armazenamento de prémio único da largura de banda e IOPS, utilize vários discos de premium riscas em conjunto. Por exemplo, faixa dois P30 discos para obter uma IOPS combinado de IOPS 10.000 ou de caudal combinado 400 MB por segundo. Como é explicado na secção seguinte, tem de utilizar um tamanho da memória virtual que suporta o combinado IOPS e o débito do disco.

>**Nota:**  
>À medida que aumenta IOPS ou débito o outro também aumenta, certifique-se de que não visitas débito ou os limites IOPS do disco ou VM quando aumentar deles.

Para assistir os efeitos de tamanho de es no desempenho da aplicação, pode executar ferramentas avaliação comparativa no seu VM e discos. Criar múltiplas teste será executado e utilizar o tamanho de es diferente para cada execução para ver o impacto. Consulte a secção [Benchmarking](#Benchmarking) no final deste artigo para obter mais detalhes.

## <a name="high-scale-vm-sizes"></a>Escala de alta VM tamanhos  
Quando começar a estruturar uma aplicação, uma das primeira coisas para fazer é, selecione uma VM para alojar o seu pedido. Armazenamento de Premium vem com tamanhos de alta VM de escala que podem executar as aplicações que requerem mais elevado cluster power e um disco local alto desempenho e/s. Estes VMs fornecem processadores mais rápidos, um rácio de memória-para-core mais elevado e um Solid-State unidade (SSD) para o disco local. Exemplos de alta escala VMs Premium armazenamento de suporte são a série DS, DSv2 e s VMs.

Alto escala VMs estão disponíveis no diferentes tamanhos com um número diferente de CPU núcleos, memória, SO e tamanho do disco temporário. Cada tamanho da memória virtual também tem o número máximo de discos de dados que pode anexar à VM. Por conseguinte, o tamanho da memória virtual que selecionou irá afetar a quantidade de processamento, memória, e capacidade de armazenamento está disponível para a sua aplicação. -Lo também afeta o cluster e dos custos de armazenamento. Por exemplo, se as especificações do tamanho da memória virtual maior numa série de DS, DSv2 série e uma série s:

| Tamanho da memória virtual | Núcleos CPU | Memória | Tamanhos de discos VM | Máximo. discos de dados | Tamanho da cache | IOPS | Limites de Cache IO de largura de banda |
|---|---|---|---|---|---|---|---|
| Standard_DS14 | 16 | 112 GB | SO = 1023 GB <br> Local SSD = 224 GB | 32 | 576 GB | 50.000 IOPS <br> 512 MB por segundo | 4.000 IOPS e 33 MB por segundo |
| Standard_GS5 | 32 | 448 GB | SO = 1023 GB <br> Local SSD = 896 GB | 64 | 4224 GB | 80.000 IOPS <br> 2000 MB por segundo | 5.000 IOPS e 50 MB por segundo |

Para ver uma lista completa de todos os tamanhos Azure VM disponíveis, consulte a [Windows VM tamanhos](../virtual-machines/virtual-machines-windows-sizes.md) ou [Linux VM tamanhos](../virtual-machines/virtual-machines-linux-sizes.md). Escolha um tamanho da memória virtual que pode reunir e dimensionar com os seus requisitos de desempenho da aplicação pretendida. Para além disso, tomar em consideração quando escolher tamanhos VM considerações importantes a seguir.


*Limites de escala*  
Os limites máximos de IOPS por VM e por disco são diferentes e independentes uns dos outros. Certifique-se de que a aplicação está a condicionar IOPS dentro dos limites da VM, bem como os discos premium anexados. Caso contrário, o desempenho da aplicação irá deparar limitação.

Por exemplo, suponha que um requisito de aplicação é um máximo de 4.000 IOPS. Para realizar esta, aprovisionar um disco P30 numa VM DS1. O disco P30 pode entregar IOPS até 5000. No entanto, a VM DS1 está limitada a 3,200 IOPS. Consequentemente, o desempenho da aplicação irá ser restringido por limite de VM na 3,200 IOPS e haverá degradação do desempenho. Para evitar esta situação, escolha um tamanho de disco e VM irá ambos cumprir os requisitos de aplicação.

*Custo da operação*  
Em muitos casos, é possível que o custo geral de operação a utilizar o armazenamento de Premium for inferior a utilizar o armazenamento padrão.

Por exemplo, considere uma aplicação que exige o 16.000 IOPS. Para realizar este desempenho, terá um padrão de\_D14 Azure IaaS VM, que dá uma IOPS máximo de 16.000 utilizando 32 discos de 1TB de armazenamento padrão. Cada disco de armazenamento padrão de 1TB possível alcançar um máximo de 500 IOPS. O valor estimado deste VM por mês serão $1,570. O custo mensal de 32 discos armazenamento padrão serão $1,638. O custo mensal total estimado serão $3,208.

No entanto, se alojado a mesma aplicação armazenamento Premium, é necessário um tamanho VM mais pequeno e menos discos de armazenamento premium, que reduz o custo geral. Um padrão de\_DS13 VM pode cumprir os requisitos de IOPS 16.000 utilizando quatro P30 discos. A VM DS13 tem um IOPS máximo de 25,600 e cada disco P30 tem um IOPS máximo de 5.000. Resumindo, esta configuração possível alcançar 5.000 x 4 = 20.000 IOPS. O valor estimado deste VM por mês serão 1,003 $por. O custo mensal de discos de armazenamento de premium P30 quatro serão $544.34. O custo mensal total estimado serão $1,544.

Tabela abaixo resume a desagregação custo deste cenário de padrão e de armazenamento de Premium.

| | **Padrão** | **Premium** |
|---|---|---|
| **Custo da VM por mês** | $1,570.58 (padrão\_D14)   | $1,003.66 (padrão\_DS13) |
| **Custo de discos por mês** | $1,638.40 (discos 32 x 1 TB) | $544.34 (4 x P30 discos) |
| **Custo geral por mês**  | $3,208.98 | $1,544.34 |

*Linux Distros*  

Com o armazenamento do Azure Premium, obtém os mesmos níveis de desempenho para VMs que executem o Windows e Linux. Suportamos existem muitos tipos de Linux distros e pode ver a lista completa de [aqui](../virtual-machines/virtual-machines-linux-endorsed-distros.md). É importante ter em atenção que distros diferentes melhor são adequados para diferentes tipos de cargas de trabalho. Irá ver as diferentes níveis de desempenho dependendo distro que sua carga de trabalho está em execução. Testar a distros Linux com a aplicação e selecione aquele que melhor se adequa.


Quando executar Linux com armazenamento Premium, verifique as atualizações mais recentes sobre controladores necessários para se certificar de alto desempenho.

## <a name="premium-storage-disk-sizes"></a>Tamanhos de disco de armazenamento Premium  
Armazenamento de Premium Azure oferece três tamanhos de discos atualmente. Cada tamanho de disco tem um limite de escala diferente para IOPS, largura de banda e armazenamento. Selecione o tamanho do disco de armazenamento de Premium consoante os requisitos da aplicação e a escala de alta tamanho da memória virtual direita. A tabela abaixo mostra os tamanhos de três discos e das suas capacidades.

| **Tipo de disco**       | **P10**           | **P 20**           | **P30**           |
|---------------------|-------------------|-------------------|-------------------|
| Tamanho do disco           | Beça 128           | 512 beça           | Beça 1024 (1 TB)   |
| IOPS por disco       | 500               | 2300              | 5000              |
| Débito por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo |

Quantos discos optar por depende do disco tamanho escolhido. Pode utilizar um único disco P30 ou vários P10 discos para cumprir o requisito de aplicação. Ter em conta considerações listadas abaixo quando efetuar a escolha.

*Limites de escala (IOPS e débito)*  
Os limites do IOPS e o débito do tamanho do disco cada Premium é diferente e independente dos limites de escala VM. Certifique-se de que o total IOPS e débito de discos estão dentro dos limites de escala do tamanho da memória virtual que selecionou.

Por exemplo, se a necessidade de uma aplicação é um máximo de 250 MB/seg débito e estiver a utilizar uma VM DS4 com um único P30 disco. A VM DS4 pode dar até 256 MB/seg débito. No entanto, um único disco P30 tem limite de débito de 200 MB/seg. Por conseguinte, a aplicação vai ser restringida na 200 MB/seg devido o limite de disco. Para ultrapassar esse limite, Aprovisione a mais do que um discos de dados para a VM.

>**Nota:**  
>Lê fornecido pela cache não é incluídas nas débito, por conseguinte, não sujeito para limites de disco e disco IOPS. Cache tem o limite IOPS e débito separado por VM.
>
>Por exemplo, inicialmente seu lê e escritas são 60MB/seg e 40MB/seg respetivamente. Ao longo do tempo, a cache warms para cima e serve mais o lê da cache de. Em seguida, pode obter mais elevada escrita débito do disco.

*Número de discos*  
Determine o número de discos que terá de por avaliação requisitos da aplicação. Cada tamanho da memória virtual também tem um limite no número de discos que pode anexar à VM. Normalmente, isto é duas vezes o número de núcleos. Certifique-se de que o tamanho da memória virtual que escolher pode suportar o número de discos conforme necessário.

Lembre-se de que os discos Premium armazenamento têm mais elevadas capacidades de desempenho em comparação com discos de armazenamento padrão. Por isso, se estiver a migrar a aplicação a partir do Azure IaaS VM utilizando o padrão de armazenamento ao armazenamento de Premium, provavelmente terá menos premium discos para alcançar o desempenho do mesmo ou superior para a sua aplicação.

## <a name="disk-caching"></a>Cache de disco  
Alto VMs de escala que tirar partido de armazenamento do Windows Azure Premium tem uma tecnologia de colocação em cache de várias camada denominada BlobCache. BlobCache utiliza uma combinação de RAM Máquina Virtual e local SSD para colocação em cache. Esta cache está disponível para o armazenamento de Premium persistente discos e o VM local. Por predefinição, esta definição de cache está definida para leitura/escrita para discos SO e só de leitura para dados discos alojados no armazenamento Premium. Com a cache de disco ativado nos discos armazenamento Premium, a escala de alta VMs possível alcançar extremamente elevados níveis de desempenho que excedam o desempenho do disco subjacente.

>[AZURE.WARNING] Alterar as definições de cache de um disco Azure separa e voltar anexa o disco de destino. Se for o disco do sistema operativo, a VM é reiniciada. Pare de todas as aplicações/serviços que podem ser afetados por este interrupção antes de alterar as definições de cache do disco.

Para saber mais sobre como funciona o BlobCache, referir-se para o interior [Azure Premium armazenamento](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) mensagem no blogue.

É importante ativar a cache do conjunto de à direita de discos. Vai ser processamento se deverá ativar a cache de disco num disco premium ou não será dependem o padrão de carga de trabalho que o disco. Tabela abaixo mostra a predefinição de definições de cache para discos SO e dados.

| **Tipo de disco** | **Predefinição de Cache** |
|---|---|
| Disco SO | ReadWrite |
| Disco de dados | Nenhum |

Seguem-se as definições de cache de disco recomendado para discos de dados,

| **Definição de colocação em cache do disco** | **Recomendação sobre quando utilizar esta definição** |
|---|---|
| Nenhum | Configure a cache de anfitrião como nenhum para discos só de leitura e escrita grossa. |
| Só de leitura | Configure o anfitrião cache como só de leitura para discos só de leitura e de leitura e escrita. |
| ReadWrite | Configure o anfitrião cache como ReadWrite apenas se a sua aplicação processa corretamente escrever dados em cache para discos persistentes quando for necessário. |

*Só de leitura*  
Configurando a colocação em cache de dados de armazenamento de Premium discos de só de leitura, pode alcançar baixa latência de leitura e obter muito alta IOPS de leitura e débito para a sua aplicação. Este é dois motivos, de conclusão

1.  Lê executado da cache, que se encontra na memória VM e local SSD, são muito mais rápido que lê a partir do disco de dados, que é o armazenamento de Blobs do Azure.  
2.  Armazenamento de Premium não contam o lê servido da cache, no sentido do disco IOPS e o débito. Por conseguinte, a aplicação é possível alcançar mais elevados IOPS total e o débito.

*ReadWrite*  
Por predefinição, os discos de SO têm ReadWrite colocação em cache ativado. Foram adicionadas recentemente suporte para ReadWrite colocação em cache dados discos também. Se estiver a utilizar ReadWrite colocação em cache, tem de ter uma forma inicial de escrever os dados da cache de discos persistentes. Por exemplo, SQL Server trata escrever dados em cache para os discos armazenamento persistente na sua própria. Utilizar ReadWrite cache com uma aplicação que não processar persistência os dados necessários pode conduzir a perda de dados, se a VM falha.

Por exemplo, pode aplicar estas diretrizes para o SQL Server em execução no armazenamento Premium ao efetuar o seguinte

1.  Configure a cache de "Só de leitura" num discos de armazenamento de premium alojamento ficheiros de dados.  
    um.  O fast lê da cache inferior a hora de consulta do SQL Server uma vez que as páginas de dados são obtidas os muito mais rapidamente da cache de comparado com diretamente a partir dos dados de discos.  
    b.  Servir lê da cache, significa que não existe débito adicional disponível a partir de discos de dados premium. SQL Server pode utilizar este débito adicional no sentido de obtenção de mais páginas de dados e outras operações como cópia de segurança/restauro, batch cargas e recria o índice remissivo.  
2.  Configurar "Nenhum" em cache no discos de armazenamento de premium que aloja os ficheiros de registo.  
    um.  Ficheiros de registo tem principalmente escrita grossa operações. Por conseguinte, não beneficiar da cache de só de leitura.

## <a name="disk-striping"></a>Repartição do disco  
Quando uma escala de alta que VM é anexado com vários discos persistente de armazenamento premium, discos podem riscas em conjunto para agregar os respetivos IOPs, a largura de banda e a capacidade de armazenamento.

No Windows, pode utilizar espaços de armazenamento a faixa discos em conjunto. Tem de configurar uma coluna para cada disco num conjunto de dados. Caso contrário, o desempenho global do volume às riscas pode ser menor que o esperado, devido a distribuição desigual de tráfego entre discos.

Importante: Utilizar o Gestor de servidor de IU, pode definir o número total de colunas até 8 para às riscas volume. Quando a anexar mais do que 8 discos, utilize o PowerShell para criar o volume. Utilizar o PowerShell, pode definir o número de colunas igual ao número de discos. Por exemplo, se existirem 16 discos num conjunto de faixa única; Especifique 16 colunas no parâmetro *NumberOfColumns* do cmdlet do PowerShell *New-VirtualDisk* .


No Linux, utilize o utilitário MDADM a faixa discos em conjunto. Para obter passos detalhados sobre repartição discos no Linux referir-se a [Configurar o Software RAID no Linux](../virtual-machines/virtual-machines-linux-configure-raid.md).


*Tamanho da faixa*  
Uma configuração importante no repartição do disco é o tamanho da faixa. O tamanho da faixa ou bloco é menor segmento de dados que pode também abordar num volume às riscas aplicação. O tamanho da faixa que configura depende do tipo de aplicação e respetivo padrão do pedido. Se escolher o tamanho da faixa mal, pode originar alinhamento IO, que produz degradação do desempenho da sua aplicação.

Por exemplo, se um pedido de IO gerado por sua aplicação for maior que o tamanho da faixa disco, o sistema de armazenamento escreve-lo ao longo dos limites de unidade de faixa no mais do que um disco. Quando está na altura de aceder a esses dados, terá de atingir através de mais do que uma faixa unidades para concluir o pedido. O efeito cumulativo deste comportamento pode conduzir a degradação do desempenho substanciais. Por outro lado, se o tamanho do pedido IO for menor que o tamanho da faixa e se for aleatório no natureza, os pedidos de IO podem adicionar para cima no mesmo disco a causar um congestionamento e finalmente prejudique o desempenho de es.


Dependendo do tipo de carga de trabalho a sua aplicação está em execução, escolha um tamanho de faixa adequado. Para pedidos de IO pequenas aleatórios, utilize um tamanho de faixa mais pequeno. Considerando que, para grande es sequencial pedidos de utilizam um tamanho de faixa maior. Descubra as faixa recomendações para o tamanho para a aplicação que irá estar em execução no armazenamento Premium. Para o SQL Server, configure o tamanho de faixa de 64KB das cargas de trabalho OLTP e KB 256 para cargas de trabalho de armazenamento de dados. Consulte o artigo [melhores práticas de desempenho para SQL Server no Azure VMs](../virtual-machines/virtual-machines-windows-sql-performance.md#disks-and-performance-considerations) para obter mais informações.


>**Nota:**  
>Pode faixas em conjunto um máximo de 32 premium armazenamento discos numa série de DS VM e 64 premium armazenamento numa série s VM.

## <a name="multi-threading"></a>Multithreading  
Azure concebido plataforma de armazenamento de Premium para ser previstos excederem consideravelmente paralelo. Por conseguinte, uma aplicação multithreaded atinge desempenho muito superior de uma aplicação por tópicos único. Divide-se às suas tarefas de entre vários threads e aumenta a eficiência da sua execução por recorrendo os VM e recursos do disco para o número máximo de uma aplicação multithreaded.

Por exemplo, se a sua aplicação está em execução numa única core VM utilizar dois threads, a CPU pode alternar entre dois threads para alcançar a eficiência. Enquanto um tópico está à espera num disco es para concluir a CPU pode mudar para o outro módulo. Desta forma, podem efetuar dois threads mais do que um único tópico seria. Se a VM tiver mais do que um core, ainda mais diminui tempo de execução uma vez que cada core pode executar tarefas em paralelo.

Não poderá alterar a forma como uma aplicação disponíveis implementa única multithreading ou módulos. Por exemplo, SQL Server é capaz de tratamento de multi CPU e com várias core. No entanto, SQL Server decide as condições nas quais-lo será tirar partido de um ou mais threads para processar uma consulta. Pode executar consultas e criar índices utilizando multithreading. Para uma consulta que envolve participar tabelas grandes e ordenar dados antes de devolver ao utilizador, SQL Server provável que irá utilizar vários threads. No entanto, um utilizador não é possível controlar se o SQL Server executa uma consulta através de um tópico única ou vários threads.

Existem definições de configuração que podem ser alteradas influenciar isto multithreading ou paralelas processamento de uma aplicação. Por exemplo, em caso de SQL Server é a configuração do grau de paralelismo máxima. Esta definição denominada MAXDOP, permite-lhe configurar o número máximo de processadores que do SQL Server pode utilizar quando paralelas de processamento. Pode configurar MAXDOP para consultas individuais ou operações do índice. Isto é útil quando pretende equilibrar recursos do sistema para uma aplicação crítico de desempenho.

Por exemplo, diga a sua aplicação utilizando o SQL Server está a executar uma consulta grande e uma operação de índice remissivo ao mesmo tempo. Diga-nos partem do princípio que pretendia que a operação de índice remissivo para ser mais performant comparada com a consulta de grandes dimensões. Neste caso, pode definir valor MAXDOP da operação de índice remissivo para ser superior ao valor MAXDOP para a consulta. Desta forma, SQL Server tem mais número de processadores-pode tirar partido para a operação de índice remissivo em comparação com o número de processadores-pode dedicar à consulta grande. Lembre-se de que não pode controlar o número de threads do SQL Server irá utilizar para cada operação. Pode controlar o número máximo de processadores a ser dedicado para multithreading.

Saiba mais sobre [Graus de paralelismo](https://technet.microsoft.com/library/ms188611.aspx) no SQL Server. Descubra essas definições que influenciam multithreading na sua aplicação e as respectivas configurações para otimizar o desempenho.

## <a name="queue-depth"></a>Fila de espera profundidade  
O profundidade fila ou o comprimento da fila ou o tamanho de fila é o número de pedidos de IO pendentes no sistema. O valor de profundidade fila determina quantos operações es a aplicação pode linha para cima, qual vai processar discos de armazenamento. -Afeta todos os indicadores de desempenho três aplicação debatido no como neste artigo, IOPS, débito e latência.

Fila de espera profundidade e são multithreading estreitamente relacionados. O valor de profundidade fila indica quanto multithreading podem ser realizado pela aplicação. Se a profundidade fila for grande, aplicação pode executar operações mais em simultâneo, por outras palavras, mais multithreading. Se a profundidade fila for pequena, apesar de aplicação está multithreaded, não terá suficiente pedidos alinhados para execução em simultâneo.

Normalmente, desativar prateleira aplicações não lhe permitir alterar profundidade fila de espera, uma vez que se definir incorretamente executará mais danos que indicado. Aplicações irão defina o valor à direita de profundidade fila de espera para obter um desempenho ideal. No entanto, é importante compreender este conceito de modo a que pode resolver problemas de desempenho com a aplicação. Também pode observar os efeitos de profundidade fila ao executar a avaliação comparativa ferramentas no sistema de.

Algumas aplicações fornecem definições para influenciar a profundidade fila de espera. Por exemplo, a definição de (máximo grau de paralelismo) MAXDOP no SQL Server explicado na secção anterior. MAXDOP é uma forma de influência fila profundidade e multithreading, apesar de não alterar diretamente o valor de profundidade fila do SQL Server.

*Fila de espera alta profundidade*  
Linhas de uma profundidade fila alta o mais operações no disco. O disco sabe o pedido seguinte na fila de antecedência. Por conseguinte, o disco pode agendar operações antecedência e processá-las numa sequência ideal. Uma vez que a aplicação está a enviar pedidos de mais para o disco, o disco pode processar IOs paralelas mais. Finalmente, a aplicação poderão alcançar IOPS superior. Uma vez que está a aplicação de processar mais pedidos, também aumenta o débito total da aplicação.

Normalmente, uma aplicação possível alcançar débito máximo com 8-16 + pendentes IOs por disco ligado. Se uma profundidade fila de espera, aplicação não é conduza suficiente IOs ao sistema e processará menor quantidade de num determinado período. Por outras palavras, menos débito.

Por exemplo, no SQL Server, definir o valor MAXDOP para uma consulta para "4" informa o SQL Server que pode utilizar o núcleos até quatro para executar a consulta. SQL Server irá determinar o que é o valor de profundidade fila melhor e o número de tarolos para a execução da consulta.

*Fila de espera ideal profundidade*  
Valor de profundidade fila muito alta também tem o respetivas desvantagens. Se o valor de profundidade fila for demasiado alto, a aplicação irá tentar unidade IOPS muito alta. A menos que a aplicação tem discos persistentes com IOPS aprovisionada suficientes, isto pode afectar negativa latências de aplicação. Fórmula a seguir mostra a relação entre IOPS, latência e profundidade fila de espera.  
    ![](media/storage-premium-storage-performance/image6.png)

Não deve configurar fila profundidade a qualquer valor alto, mas a um valor de ideal, pode entregar suficiente IOPS para a aplicação sem afetar latências. Por exemplo, se a latência da aplicação tem de estar 1 milissegundos, a fila profundidade necessário para alcançar 5.000 IOPS for, QD = 5000 x 0,001 = 5.

*Fila de espera profundidade para alterar o Volume às riscas*  
Para um volume às riscas, manter uma profundidade fila suficientemente alta assim que cada disco tem uma profundidade de fila de pico individualmente. Por exemplo, considere uma aplicação que envia uma profundidade fila de 2 e não existem 4 discos a faixa. Os pedidos de IO duas serão direcionadas para dois discos e restante dois discos serão idle. Por conseguinte, configure a profundidade fila assim que todos os discos podem ser ocupados. Fórmula abaixo mostra como determinar a profundidade fila de volumes às riscas.  
    ![](media/storage-premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitação  
Azure disposições de Premium armazenamento especificados número de IOPS e débito consoante o tamanhos VM e tamanhos de disco que escolher. Sempre a sua aplicação tenta unidade IOPS ou débito acima estes limites de que a VM ou o disco se pode processar, o armazenamento de Premium optimizá-lo. Isto manifestos sob a forma de desempenho degradado na sua aplicação. Isto pode vermelha latência superior, inferior débito ou baixar IOPS. Se não optimizar a armazenamento Premium, a aplicação completamente poderá falhar ao excedem o que são capazes de atingir seus recursos. Sim, para evitar problemas de desempenho devido a limitação, sempre Aprovisione o recursos suficientes para a sua aplicação. Tenha em consideração o que debatido nas secções de tamanhos de disco acima e tamanhos VM. Direccionamento de caminhos é a melhor forma de descobriu quais os recursos que terá a sua aplicação do anfitrião.

## <a name="benchmarking"></a>Análise comparativa  
Análise comparativa é o processo de simulação das cargas de trabalho diferentes na sua aplicação e medir o desempenho de aplicação para cada carga de trabalho. Utilizar os passos descritos numa secção anterior, recolheu os requisitos de desempenho de aplicação. Ao executar a avaliação comparativa ferramentas no VMs que aloja a aplicação, pode determinar os níveis de desempenho que a aplicação possível alcançar com armazenamento Premium. Nesta secção, fornecemos-lhe exemplos de direccionamento de caminhos uma VM DS14 padrão aprovisionados com discos de armazenamento do Azure Premium.

Utilizámos ferramentas avaliação comparativa comuns Iometer e FIO, para o Windows e Linux respetivamente. Estas ferramentas geram vários threads simular uma produção como carga de trabalho e medem o desempenho do sistema. Utilizar as ferramentas de também pode configurar parâmetros, tais como bloquear tamanho e fila de profundidade, que normalmente não é possível alterar para uma aplicação. Isto dá-lhe mais flexibilidade para orientar o máximo desempenho numa escala de alta VM aprovisionado com discos premium para diferentes tipos de cargas de trabalho de aplicação. Para obter mais informações sobre cada ferramenta avaliação comparativa visite [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie uma VM DS14 padrão e anexar 11 discos de armazenamento de Premium para a VM. Os discos 11, configurar 10 discos com anfitrião colocação em cache como "Nenhum" e faixas-los para um volume chamado NoCacheWrites. Configurar anfitrião como "Só de leitura" colocação em cache no disco restante e crie um volume chamado CacheReads com este disco. Utilizar este programa de configuração, poderão ver o desempenho máximo de leitura e escrita a partir de uma VM DS14 padrão. Para obter passos detalhados sobre como criar uma VM DS14 com discos premium, consulte [criar e utilizar a conta de armazenamento de Premium para um disco de dados de máquina virtual](storage-premium-storage.md#create-and-use-a-premium-storage-account-for-a-virtual-machine-data-disk).

*Preparar a Cache*  
O disco com o anfitrião só de leitura colocação em cache poderão dar IOPS mais elevados que o limite de disco. Para obter este máximo desempenho de leitura da cache de anfitrião, pela primeira vez, tem interessado para cima a cache deste disco. Este procedimento assegura que o IOs leitura qual a ferramenta avaliação comparativa irá unidade no CacheReads volume realmente acertos a cache e não o disco diretamente. O resultado de acertos cache na IOPS adicionais da cache de única ativada disco.

>**Importante:**  
>Tem interessado antes de executar o direccionamento de caminhos, sempre que for reiniciado VM a cache.

#### <a name="iometer"></a>Iometer   
[Transfira a ferramenta de Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) a VM.

*Ficheiro de teste*  
Iometer utiliza um ficheiro de teste que está armazenado no volume no qual será executado o teste de avaliação comparativa. -Unidades lê e escreve neste ficheiro de teste para medir o disco IOPS e débito. Iometer cria este ficheiro de teste se não tiver fornecido um. Crie um ficheiro de teste de 200GB designado por iobw.tst os volumes de CacheReads e NoCacheWrites.

*Especificações do Access*  
Especificações, pedir o tamanho de es, % leitura/escrita, % aleatório/sequenciais estão configurados através do separador "Especificações do Access" Iometer. Crie uma especificação de acesso para cada um dos cenários descritos abaixo. Crie as especificações do access e "Guardar" com um adequado nome como – RandomWrites\_8K, RandomReads\_8K. Selecione a especificação de correspondente ao executar o cenário de teste.

Um exemplo de especificações do access para máximo cenário de escrever IOPS é mostrado abaixo,  
    ![](media/storage-premium-storage-performance/image8.png)

*Especificações de ensaio IOPS máximo*  
Para demonstrar IOPs máximos, utilize o tamanho mais pequeno do pedido. Utilize o tamanho do pedido 8K e crie especificações para escreve aleatório e lê.

| Especificação do Access | Tamanho do pedido | Aleatório % | % De leitura |
|----------------------|--------------|----------|--------|
| RandomWrites\_K de 8     | 8K           | 100      | 0      |
| RandomReads\_K de 8      | 8K           | 100      | 100    |

*Especificações de teste de débito máximo*  
Para demonstrar débito máximo, utilize o tamanho de pedido maior. Utilize o tamanho do pedido 64 mil e crie especificações para escreve aleatório e lê.

| Especificação do Access | Tamanho do pedido | Aleatório % | % De leitura |
|----------------------|--------------|----------|--------|
| RandomWrites\_64K    | 64 MIL          | 100      | 0      |
| RandomReads\_64K     | 64 MIL          | 100      | 100    |

*A executar o teste de Iometer*  
Execute os passos abaixo para interessado cache

1.  Criar duas especificações do access com valores mostrados abaixo,

  	| Nome              | Tamanho do pedido | Aleatório % | % De leitura |
  	|-------------------|--------------|----------|--------|
  	| RandomWrites\_1MB | 1MB          | 100      | 0      |
  	| RandomReads\_1MB  | 1MB          | 100      | 100    |

2.  Execute o teste de Iometer para a inicialização disco na cache com seguintes parâmetros. Utilize três threads de trabalho para o volume de destino e uma profundidade fila de 128. Defina a duração "Tempo de execução" do teste para 2hrs no separador "Testar o programa de configuração".

  	| Cenário              | Volume de destino | Nome              | Duração |
  	|-----------------------|---------------|-------------------|----------|
  	| Iniciar o disco na Cache | CacheReads    | RandomWrites\_1MB | 2hrs     |

3.  Execute o teste de Iometer para aquecimento disco na cache com seguintes parâmetros. Utilize três threads de trabalho para o volume de destino e uma profundidade fila de 128. Defina a duração "Tempo de execução" do teste para 2hrs no separador "Testar o programa de configuração".

  	| Cenário           | Volume de destino | Nome             | Duração |
  	|--------------------|---------------|------------------|----------|
  	| Quente o disco na Cache | CacheReads    | RandomReads\_1MB | 2hrs     |

Depois de disco na cache está sujeito a adequado aquecimento, prosseguir com os cenários de teste listados abaixo. Para executar o teste de Iometer, utilize pelo menos três threads de trabalho para **cada** volume de destino. Para cada módulo de trabalho, selecione o volume de destino, definir fila de profundidade e selecione uma das especificações de teste guardada, conforme apresentado na tabela abaixo, para executar o cenário de teste correspondente. A tabela também mostra os resultados esperados para IOPS e débito quando estes testes a ser executado. Para todos os cenários, um tamanho de es pequenas de 8KB e uma profundidade alta fila de 128 são utilizados.

| Cenário de teste      | Volume de destino | Nome              | Resultado       |
|--------------------|---------------|-------------------|--------------|
| Máximo. Leia IOPS     | CacheReads    | RandomWrites\_K de 8  | 50.000 IOPS  |
| Máximo. Escrever IOPS    | NoCacheWrites | RandomReads\_K de 8   | 64.000 IOPS  |
| Máximo. IOPS combinado | CacheReads    | RandomWrites\_K de 8  | 100.000 IOPS |
|                    | NoCacheWrites | RandomReads\_K de 8   |              |
| Máximo. Ler MB/seg   | CacheReads    | RandomWrites\_64K | 524 MB/seg   |
| Máximo. Escrever MB/seg  | NoCacheWrites | RandomReads\_64K  | 524 MB/seg   |
| Combinado MB/seg    | CacheReads    | RandomWrites\_64K | 1000 MB/seg.  |
|                    | NoCacheWrites | RandomReads\_64K  |              |

Abaixo estão capturas de ecrã da Iometer testar resultados para o combinado cenários IOPS e débito.

*Lê combinado e escritas IOPS máximo*  
![](media/storage-premium-storage-performance/image9.png)

*Lê combinado e escritas débito máximo*  
![](media/storage-premium-storage-performance/image10.png)

### <a name="fio"></a>FIO  
FIO é uma ferramenta popular ao armazenamento de teste de referência na VMs Linux. Tem a flexibilidade de poder selecionar diferentes tamanhos de IO, sequenciais ou lê aleatório e escreve. -Expande threads de trabalho ou processos para executar as operações e/s especificadas. Pode especificar o tipo de operações e/s que cada tópico de trabalho deve efetuar utilizar ficheiros de trabalho. Criámos um ficheiro de tarefa por cenário ilustrado nos exemplos abaixo. Pode alterar as especificações estes ficheiros de trabalho para diferentes das cargas de trabalho em execução no Premium armazenamento de referência. Nos exemplos, estamos a utilizar um padrão VM de 14 DS executar **Ubuntu**. Utilize a mesma configuração descrita no início da [secção Benchmarking](#Benchmarking) floral e cheio até a cache antes de executar os testes de desempenho.

Antes de começar, [Transfira o FIO](https://github.com/axboe/fio) e instalá-lo no seu computador virtual.

Execute o seguinte comando para Ubuntu,

        apt-get install fio

Irá utilizamos quatro threads de trabalho para conduzir a operações de escrita e quatro threads de trabalho para operações de leitura condução nos discos. Os colaboradores na área de escrita serão condução tráfego no volume "nocache", que possui 10 discos com a cache definida como "Nenhuma". Os trabalhadores de leitura serão condução tráfego no volume "readcache", que possui 1 disco com conjunto de cache para "Só de leitura".

*Escrever máximo IOPS*  
Crie o ficheiro de tarefa com seguintes especificações para obter máximo IOPS escrever. Atribuir um nome "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Tenha em atenção os aspetos de chave seguir que estão em linha com as diretrizes de estrutura abordadas no secções anteriores. Estas especificações são essenciais para orientar IOPS máximo,  
-   Uma profundidade alta fila de 256.  
-   Um tamanho pequeno bloco de 8KB.  
-   Múltiplos threads efetuar aleatório escreve.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,  

    sudo fio --runtime 30 fiowrite.ini

Enquanto executa o teste, vai conseguir ver o número de escrever IOPS a VM e estiver a realizar a discos Premium. Como é mostrado no exemplo abaixo, a VM DS14 está a entregar o seu limite IOPS de 50.000 IOPS de escrita máxima.  
    ![](media/storage-premium-storage-performance/image11.png)

*Leitura máxima IOPS*  
Crie o ficheiro de tarefa com seguintes especificações para obter máximo IOPS de leitura. Atribuir um nome "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Tenha em atenção os aspetos de chave seguir que estão em linha com as diretrizes de estrutura abordadas no secções anteriores. Estas especificações são essenciais para orientar IOPS máximo,

-   Uma profundidade alta fila de 256.  
-   Um tamanho pequeno bloco de 8KB.  
-   Múltiplos threads efetuar aleatório escreve.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,

    sudo fio --runtime 30 fioread.ini

Enquanto executa o teste, vai conseguir ver o número de ler IOPS a VM e estiver a realizar a discos Premium. Como é mostrado no exemplo abaixo, a VM DS14 está a entregar mais do que 64.000 IOPS de leitura. Esta é uma combinação do disco e o desempenho da cache.  
    ![](media/storage-premium-storage-performance/image12.png)

*Máximo de leitura e escrita IOPS*  
Criar o ficheiro de tarefa com seguinte especificações para obter máximo combinados ler e escrever IOPS. Atribuir um nome "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Tenha em atenção os aspetos de chave seguir que estão em linha com as diretrizes de estrutura abordadas no secções anteriores. Estas especificações são essenciais para orientar IOPS máximo,

-   Uma profundidade alta fila de 128.  
-   Um tamanho pequeno bloco de 4KB.  
-   Múltiplos threads efetuar aleatório lê e escreve.

Execute o seguinte comando para iniciar o teste FIO para 30 segundos,

    sudo fio --runtime 30 fioreadwrite.ini

Enquanto executa o teste, poderão ver o número de combinado leitura e escrita IOPS a VM e estiver a realizar a discos Premium. Como é mostrado no exemplo abaixo, a VM DS14 é fornecer mais do que 100.000 leitura combinado e escrever IOPS. Esta é uma combinação do disco e o desempenho da cache.  
    ![](media/storage-premium-storage-performance/image13.png)

*Máximo combinados débito*  
Para obter o valor máximo combinados ler e escrever débito, utilize um tamanho bloco maior e profundidade fila grande com vários threads efetuar operações de leitura e escrita. Pode utilizar um tamanho de bloco de 64KB e profundidade fila de 128.

## <a name="next-steps"></a>Próximos passos  

Saiba mais sobre o armazenamento do Windows Azure Premium:

- [Armazenamento de Premium: Armazenamento de alto desempenho para Máquina Virtual Azure cargas de trabalho](storage-premium-storage.md)  

Para utilizadores do SQL Server, leia artigos desempenho melhores práticas para SQL Server:

- [Desempenho melhores práticas para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-performance.md)
- [Azure Premium armazenamento fornece mais alto desempenho para SQL Server em máquinas Azure VM](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx) 
