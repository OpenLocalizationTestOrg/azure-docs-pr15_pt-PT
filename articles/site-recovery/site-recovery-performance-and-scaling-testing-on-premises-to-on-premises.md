<properties
    pageTitle="Teste de desempenho e a escala resulta no local para a replicação de Hyper-V no local com a recuperação de Site | Microsoft Azure"
    description="Este artigo fornece informações sobre testes de desempenho para no local para utilizar a recuperação de Site do Azure a replicação no local."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="07/06/2016"
    ms.author="raynew"/>

# <a name="performance-test-and-scale-results-for-on-premises-to-on-premises-hyper-v-replication-with-site-recovery"></a>Teste de desempenho e dimensionar resultados para no local para a replicação de Hyper-V no local com a recuperação de Site

Pode utilizar recuperação de Site do Microsoft Azure orquestrar e gerir a replicação de máquinas virtuais e servidores físicos para Azure ou para um centro de dados secundário. Este artigo fornece os resultados de testes de desempenho que fizemos quando replicação máquinas virtuais de Hyper-V entre dois locais centros de dados.



## <a name="overview"></a>Descrição geral

O propósito de testes foi examinar como recuperação de sites do Azure executa durante a replicação de estado estável. Estado estável a replicação ocorre quando máquinas virtuais tiver concluído a replicação inicial e estiver a sincronizar alterações delta. É importante medir o desempenho com o estado estável porque é o estado em que a maioria dos máquinas virtuais permanecem, a menos que ocorrem falhas inesperadas.


A implementação de teste a ser constituído por dois sites no local com um servidor de VMM cada site. Esta implementação de teste é típica de uma implementação do office de cabeça office/ramo, com sede serve como o site principal e o office ramo como o site secundário ou recuperação.

### <a name="what-we-did"></a>O que fizemos

Eis o que recomendamos no teste passar:

1. Criado máquinas virtuais utilizando modelos VMM.

1. Introdução máquinas virtuais e captura do plano base desempenho métricas superior a 12 horas.

1. Criado nuvens em servidores VMM principal e recuperação.

1. Proteção de nuvem configurado no Azure recuperação de sites, incluindo o mapeamento das nuvens recortada de origem e recuperação.

1. Ativado proteção para máquinas virtuais e permitir que este conclua a replicação inicial.

1. Aguardado algumas horas ardido de sistema.

1. Capturado métricas de desempenho durante 12 horas, assegurar que todas as máquinas virtuais permaneceram num Estado de replicação esperado para esses 12 horas.

1. Meça a delta entre as métricas de desempenho do plano base e as métricas de desempenho de replicação.

## <a name="test-deployment-results"></a>Resultados de implementação de teste

### <a name="primary-server-performance"></a>Desempenho do servidor principal

- Hyper-V réplica modo assíncrono controla alterações a um ficheiro de registo com sobrecarga de armazenamento mínima no servidor principal.

- Hyper-V réplica utiliza a cache de memória mantida personalizada para minimizar IOPS sobrecarga para controlo. Armazena escreve VHDX na memória e esvazia-los para o ficheiro de registo antes da hora em que o registo é enviado para o site de recuperação. Um disco alinhado também acontece se o escreve visitas um limite predeterminado.

- O gráfico abaixo mostra o overhead IOPS estado estável para a replicação. É possível ver que IOPS sobrecarga devido a replicação é de cerca de 5%, que é bastante baixa.

![Resultados principais](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744913.png)

Hyper-V réplica utiliza memória no servidor principal para otimizar o desempenho do disco. Como é mostrado no seguinte graph, a memória sobrecarga em todos os servidores cluster primário é marginal. A memória sobrecarga apresentada é a percentagem de memória utilizada pelo replicação em comparação comparada o total de memória instalado no servidor Hyper-V.

![Resultados principais](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744914.png)

Hyper-V réplica tem sobrecarga da CPU mínima. Como é mostrado no gráfico, sobrecarga na replicação está no intervalo de 2-3%.

![Resultados principais](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744915.png)

### <a name="secondary-recovery-server-performance"></a>Desempenho do servidor secundário (recuperação)

Hyper-V réplica utiliza uma pequena quantidade de memória no servidor de recuperação para otimizar o número de operações de armazenamento. O gráfico resume a utilização da memória no servidor de recuperação. A memória sobrecarga apresentada é a percentagem de memória utilizada pelo replicação em comparação comparada o total de memória instalado no servidor Hyper-V.

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744916.png)

A quantidade de operações de e/s no site de recuperação é uma função do número de operações de escrita no site principal. Vamos observar as operações e/s total no site de recuperação em comparação com as operações e/s total operações e escrita no site principal. Os gráficos mostram que o total IOPS no site de recuperação é

- Em redor de 1,5 vezes a escrita IOPS na página principal.

- Cerca de 37% da IOPS total no site principal.

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744917.png)

![Resultados secundários](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744918.png)

### <a name="effect-of-replication-on-network-utilization"></a>Efeito de replicação sobre a utilização da rede

Uma média de 275 MB por segundo de largura de banda de rede foi utilizada entre os nós principal e recuperação (com a compressão activada) contra uma largura de banda existente de 5 GB por segundo.

![Resultados da utilização da rede](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744919.png)

### <a name="effect-of-replication-on-virtual-machine-performance"></a>Efeito de replicação no desempenho de máquina virtual

Uma consideração importante é o impacto das replicação das cargas de trabalho de produção em execução nas máquinas virtuais. Se o site principal adequadamente está aprovisionado para a replicação, não deva existir qualquer impacto nas cargas de trabalho. Lightweight da Hyper-V réplica rastreio mecanismo assegura que os das cargas de trabalho a ser executada em máquinas virtuais não são afectadas durante a replicação de estado estável. Isto é ilustrado nos gráficos seguintes.

Este gráfico apresenta IOPS realizado por máquinas virtuais executadas diferentes das cargas de trabalho antes e depois da replicação foi ativada. É possível observar que não existe nenhuma diferença entre os dois.

![Resultados de efeito réplica](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744920.png)

O gráfico seguinte mostra o débito do máquinas virtuais executadas diferentes das cargas de trabalho antes e depois de replicação foi ativada. É possível observar que a replicação não tem nenhum impacto significativo.

![Efeitos de réplica de resultados](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744921.png)

### <a name="conclusion"></a>Conclusão

Os resultados claramente mostram que recuperação de Site do Azure, com Hyper-V réplica, a forma escalas bem com mínimo sobrecarga para um cluster de grandes dimensões.  Recuperação de Site Azure fornece implementação simple, replicação, monitorização e gestão. Hyper-V réplica fornece a infraestrutura de necessária para dimensionamento replicação bem sucedida. Para planear uma implementação ideal sugerimos que transferir o [Planeador de capacidade de réplica Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057).

## <a name="test-environment-details"></a>Detalhes do ambiente de teste

### <a name="primary-site"></a>Site principal

- O site principal tem um cluster de cinco Hyper-V servidores a executar o 470 máquinas virtuais.

- As máquinas virtuais executar diferentes cargas de trabalho e tem todas as proteção de recuperação de sites do Azure ativada.

- Armazenamento para o nó do cluster é fornecido por um iSCSI SAN. Modelo – Hitachi HUS130.

- Cada cluster de servidor tem quatro cartões de rede (NIC) de um Gbps.

- Dois dos cartões de rede estão ligados a uma rede privada iSCSI e duas estiverem ligadas à rede empresarial externa. Uma das redes externas está reservada para apenas comunicações cluster.

![Requisitos de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744922.png)

|Servidor|RAM|Modelo|Processador|Número de processadores|NIC|Software|
|---|---|---|---|---|---|---|
|Servidores Hyper-V cluster: <br />ESTLAB HOST11<br />ESTLAB HOST12<br />ESTLAB HOST13<br />ESTLAB HOST14<br />ESTLAB HOST25|128ESTLAB HOST25 tem 256|Dell™ PowerEdge™ R820|Intel (r) Xeon(R) CPU E5-4620 0 @ 2.20GHz|4|Posso Gbps x 4|Windows Server 2012 do Centro de dados R2 (x64) + função Hyper-V|
|Servidor VMM|2|||2|1 Gbps|Windows Server 2012 da base de dados R2 (x64) + VMM 2012 R2|

### <a name="secondary-recovery-site"></a>Site secundário (recuperação)

- O site secundário tem um cluster de seis nó activação pós-falha.

- Armazenamento para o nó do cluster é fornecido por um iSCSI SAN. Modelo – Hitachi HUS130.

![Especificação de hardware principal](./media/site-recovery-performance-and-scaling-testing-on-premises-to-on-premises/IC744923.png)

|Servidor|RAM|Modelo|Processador|Número de processadores|NIC|Software|
|---|---|---|---|---|---|---|
|Servidores Hyper-V cluster: <br />ESTLAB HOST07<br />ESTLAB HOST08<br />ESTLAB HOST09<br />ESTLAB HOST10|96|Dell™ PowerEdge™ R720|Intel (r) Xeon(R) CPU E5-2630 0 @ 2.30GHz|2|Posso Gbps x 4|Windows Server 2012 do Centro de dados R2 (x64) + função Hyper-V|
|ESTLAB HOST17|128|Dell™ PowerEdge™ R820|Intel (r) Xeon(R) CPU E5-4620 0 @ 2.20GHz|4||Windows Server 2012 do Centro de dados R2 (x64) + função Hyper-V|
|ESTLAB HOST24|256|Dell™ PowerEdge™ R820|Intel (r) Xeon(R) CPU E5-4620 0 @ 2.20GHz|2||Windows Server 2012 do Centro de dados R2 (x64) + função Hyper-V|
|Servidor VMM|2|||2|1 Gbps|Windows Server 2012 da base de dados R2 (x64) + VMM 2012 R2|

### <a name="server-workloads"></a>Das cargas de trabalho do servidor

- Para fins de teste podemos recolhidos das cargas de trabalho era utilizadas frequentemente em cenários de cliente do enterprise.

- Utilizamos [IOMeter](http://www.iometer.org) com a característica de carga de trabalho é resumida na tabela para simulação.

- Todos os perfis de IOMeter estão definidos para escrever bytes aleatórios para simular pior escrever padrões das cargas de trabalho.

|Carga de trabalho|Tamanho e/s (KB)|% Do access|% De leitura|/ S pendentes|Padrão e/s|
|---|---|---|---|---|---|
|Servidor de ficheiros|48163264|60% 20 %5 %5% 10%|80% 80% 80% 80% 80%|88888|Todos os 100% aleatórios|
|SQL Server (volume 1) do SQL Server (volume 2)|864|100% 100%|70 %0%|88|% de 100% random100 sequencial|
|Exchange|32|100%|67%|8|aleatório a 100%|
|Estação de trabalho/VDI|464|66% 34%|70% 95%|11|Ambos os 100% aleatórios|
|Servidor de ficheiros de Web|4864|33% 34% 33|% de 95% de 95 95%|888|Todos os 75% aleatórios|

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

- 470 máquinas virtuais no cluster principal.

- Todas as máquinas virtuais com disco VHDX.

- Máquinas virtuais em execução das cargas de trabalho resumidas na tabela. Todos os foram criados com modelos VMM.

|Carga de trabalho|# VMs|Mínimo (GB) de RAM|Quantidade máxima de RAM (GB)|Tamanho do disco lógico (GB) por VM|IOPS máximo|
|---|---|---|---|---|---|
|Do SQL Server|51|1|4|167|10|
|Servidor do Exchange|71|1|4|552|10|
|Servidor de ficheiros|50|1|2|552|22|
|VDI|149|. 5 a|1|80|6|
|Servidor Web|149|. 5 a|1|80|6|
|TOTAL|470|||96.83 TB|4108|

### <a name="azure-site-recovery-settings"></a>Definições de recuperação de Site Azure

- Azure recuperação de Site foi configurada para no local para protecção no local

- O servidor VMM tem quatro nuvens recortada configurada, que contém os servidores de cluster Hyper-V e os respetivos máquinas virtuais.

|Nuvem VMM principal|Protegida máquinas virtuais na nuvem|Frequência de replicação|Pontos de recuperação adicionais|
|---|---|---|---|
|PrimaryCloudRpo15m|142|15 minutos|Nenhum|
|PrimaryCloudRpo30s|47|30 segundos|Nenhum|
|PrimaryCloudRpo30sArp1|47|30 segundos|1|
|PrimaryCloudRpo5m|235|5 minutos|Nenhum|

### <a name="performance-metrics"></a>Métricas de desempenho

A tabela resume as métricas de desempenho e contadores que foram medidas na implementação.

|Métrica|Contador|
|---|---|
|CPU|\Processor(_Total)\% processador de tempo|
|Memória disponível|\Memory\Available MB|
|IOPS|Transferência de \Disk \PhysicalDisk ( total) / seg|
|VM operações de leitura (IOPS) / seg|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Read operações/seg|
|Operações de escrita (IOPS) de VM/seg|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Write operações/S|
|VM ler débito|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Read Bytes/seg|
|Débito de escrita VM|Dispositivo de armazenamento virtual \Hyper-V (<VHD>) \Write Bytes/seg|


## <a name="next-steps"></a>Próximos passos

- [Configurar o proteção entre dois locais VMM no local](site-recovery-vmm-to-vmm.md)
