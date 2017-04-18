<properties
    pageTitle="Solução de gestão de capacidade no registo de análise | Microsoft Azure"
    description="Pode utilizar a solução de planeamento de capacidade no registo Analytics para ajudá-lo a compreender a capacidade das seus servidores de Hyper-V gerido pelo Gestor de Máquina Virtual do Centro de sistema"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="capacity-management-solution-in-log-analytics"></a>Solução de gestão de capacidade no registo de análise


Pode utilizar a solução de planeamento de capacidade no registo Analytics para ajudá-lo a compreender a capacidade das seus servidores de Hyper-V gerido pelo Gestor de Máquina Virtual do Centro de sistema. Esta solução requer o Gestor de operações do Centro de sistema e Gestor de Máquina Virtual do Centro de sistema. Planear a capacidade não está disponível se utilizar apenas agentes directamente ligados. Instale a solução para atualizar o agente de Gestor de operações. A solução contadores de desempenho no servidor monitorizado de lê e envia dados de utilização para o serviço OMS na nuvem para processamento. Lógica é aplicada aos dados de utilização, e o serviço de nuvem regista os dados. Ao longo do tempo, padrões de utilização são identificados e capacidade é projectada, com base em consumo atual.

Por exemplo, uma projecções poderão identificar quando núcleos processador adicional ou memória adicional será necessário para um servidor individual. Neste exemplo, a projecções podem indicar que 30 dias o servidor será necessário memória adicional. Isto pode ajudar a planear uma atualização de memória durante a janela de manutenção seguinte no servidor, que pode ocorrer em duas semanas.

>[AZURE.NOTE] A solução de gestão da capacidade não está disponível para ser adicionada à áreas de trabalho. Clientes que têm a solução de gestão de capacidade instalada podem continuar a utilizar a solução.  

A capacidade de solução para planeamento é processo de ser actualizado resolver o cliente seguinte comunicado desafios:

- Requisitos para utilizar o Gestor de Máquina Virtual e Gestor de operações
- Impossibilidade de personalizar/filtro com base em grupos
- Preço por hora agregação de dados não frequente suficiente
- Não existem informações de nível de VM
- Fiabilidade dos dados

Benefícios da nova solução capacidade:

- Suporte de recolha de dados granular com fiabilidade melhorada e precisão
- Suporte para Hyper-V sem necessidade de VMM
- Visualização de métricas de obter
- Informações sobre a utilização de nível de VM


## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

- Gestor de operações é necessário para a solução de gestão da capacidade.
- Gestor de máquina virtual é necessário para a solução de gestão da capacidade.
- É necessária conectividade de Gestor de operações com Manager Máquina Virtual (VMM). Para obter informações adicionais sobre como ligar os sistemas de, consulte o artigo [como ligar VMM com o Gestor de operações](http://technet.microsoft.com/library/hh882396.aspx).
- Gestor de operações deve estar ligado a análise de registo.
- Adicione a solução de gestão da capacidade para a área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.


## <a name="capacity-management-data-collection-details"></a>Capacidade detalhes de recolha de dados de gestão

Gestão de capacidade recolhe dados de desempenho, metadados e dados de estado utilizando os agentes de que ativou.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para a gestão da capacidade.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![N](./media/log-analytics-capacity/oms-bullet-red.png)|![Sim](./media/log-analytics-capacity/oms-bullet-green.png)|![N](./media/log-analytics-capacity/oms-bullet-red.png)|            ![Sim](./media/log-analytics-capacity/oms-bullet-green.png)|![Sim](./media/log-analytics-capacity/oms-bullet-green.png)| hora a hora|

A tabela seguinte mostra exemplos dos tipos de dados recolhidos pela gestão da capacidade:

|**Tipo de dados**|**Campos**|
|---|---|
|Metadados|BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NomeRede, endereço IP, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, endereço IP, NetbiosDomainName, LogicalProcessors, NomeDNS, DisplayName em grupos, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime|
|Desempenho|Nome de objecto, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded|
|Estado|StateChangeEventId, StateId, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, última modificação, LastGreenAlertGenerated, DatabaseTimeModified|

## <a name="capacity-management-page"></a>Página de gestão de capacidade


 Depois da solução planeamento da capacidade está instalada, pode ver a capacidade das seus servidores de monitorizada utilizando o mosaico de **Planeamento de capacidade** na página **Descrição geral** na OMS.

![imagem de mosaico de planeamento de capacidades](./media/log-analytics-capacity/oms-capacity01.png)

O mosaico abre o dashboard de **Gestão da capacidade** onde pode ver um resumo da sua capacidade de servidor. A página apresenta os mosaicos seguintes que pode clicar em:

- *Contagem de máquina virtual*: mostra o número de dias restante para a capacidade das máquinas virtuais
- *Calcular*: mostra núcleos processador e memória disponível
- *Armazenamento*: mostra o espaço em disco utilizado e fazer a média de latência no disco
- *Pesquisa*: Explorador de dados que pode utilizar para procurar todos os dados no sistema de OMS

![imagem do dashboard de gestão da capacidade](./media/log-analytics-capacity/oms-capacity02.png)


### <a name="to-view-a-capacity-page"></a>Para ver uma página de capacidade

- Na página **Descrição geral** , clique em **Gestão de capacidade**e, em seguida, clique em **Calcular** ou **armazenamento**.

## <a name="compute-page"></a>Calcular a página

Pode utilizar o dashboard **Calcular** no Microsoft Azure OMS para ver informações de capacidade sobre utilização, projetado dias da capacidade e eficiência relacionados com a sua infraestrutura. Utilize a área de **utilização** para ver a utilização de memória e core da CPU na sua anfitriões de máquina virtual. Pode utilizar a ferramenta de projecções para estimar quanto capacidade é esperada para estar disponível para um determinado intervalo de datas. Pode utilizar a área de **eficiência** para ver como eficiente os anfitriões de máquina virtual são. Pode ver os detalhes sobre itens ligados ao clicar nelas.

Pode gerar um livro do Excel para as seguintes categorias:

- Anfitriões superiores com mais alta utilização de core
- Anfitriões superiores com mais alta utilização de memória
- Anfitriões superiores com ineficazes máquinas virtuais
- Anfitriões superiores por utilização
- Anfitriões inferior por utilização

![imagem da página de gestão de capacidade calcular](./media/log-analytics-capacity/oms-capacity03.png)


As seguintes áreas são apresentadas no dashboard de **Calcular** :

**Utilização**: utilização de memória e core vista CPU no seu anfitriões de máquina virtual.

- *Utilizado núcleos*: soma para todos os anfitriões (% de utilização da CPU multiplicada pelo número de núcleos físicos no anfitrião).
- *Núcleos livre*: Total núcleos físicos menos núcleos utilizados.
- *Percentagem núcleos disponíveis*: livre núcleos físicos divididos pelo número total de núcleos físicos.
- *Virtual núcleos por VM*: Total núcleos virtuais no sistema de dividido pelo número total de máquinas virtuais do sistema.
- *Virtual núcleos física núcleos rácio*: rácio do total núcleos físicos medulas física que são utilizados pelo máquinas virtuais do sistema.
- *Número de virtual núcleos disponíveis*: core Virtual rácio núcleos física multiplicados pelos tarolos físicos disponíveis.
- *Memória utilizado*: soma de memória é utilizada por todos os anfitriões.
- *Memória livre*: Total de memória física subtração utilização de memória.
- *Percentagem memória disponível*: libertar memória física dividida por total de memória física.
- *Memória virtual por VM*: memória virtual Total no sistema de dividido pelo número total de máquinas virtuais do sistema.
- *Memória virtual física rácio de memória*: memória virtual Total no sistema de dividido pela memória física total no sistema.
- *Disponível de memória virtual*: Memória Virtual rácio de memória física multiplicada pela memória disponível física.

**Ferramenta de projecções**

Utilizando a ferramenta de projecções, pode ver tendências históricas para utilização de recursos. Isto inclui as tendências de utilização para máquinas virtuais, memória, core e armazenamento. A capacidade de projecções utiliza um algoritmo de projecções para ajudá-lo a saber quando estiver a executar o terminar cada um dos recursos. Isto ajuda a calcular capacidade inicial planeamento para que pode saber quando precisar de adquirir mais capacidade (como memória, núcleos ou armazenamento).

**Eficiência**

- *Idle VM*: com menos de 10% da memória CPU e 10% para o período de tempo especificado.
- *Overutilized VM*: utilizar mais do que 90% da memória CPU e 90% para o período de tempo especificado.
- *Idle anfitrião*: com menos de 10% da memória CPU e 10% para o período de tempo especificado.
- *Overutilized anfitrião*: utilizar mais do que 90% da memória CPU e 90% para o período de tempo especificado.

### <a name="to-work-with-items-on-the-compute-page"></a>Para trabalhar com itens na página cluster

1. No dashboard de **Calcular** , na área de **utilização** , ver informações de capacidade sobre a núcleos CPU e a utilização de memória.
2. Clique num item para abri-lo na página de **pesquisa** e ver informações detalhadas acerca do mesmo.
3. Na ferramenta de **projecções** , mova o controlo de deslize de data para apresentar uma projecções de capacidade que será utilizada na data que escolher.
4. Na área **eficiência** , ver informações de eficiência de capacidade sobre máquinas virtuais e anfitriões de máquina virtual.

## <a name="direct-attached-storage-page"></a>Página de armazenamento ligado à direta

Pode utilizar o dashboard **Direta armazenamento anexado** no OMS para ver informações de capacidade sobre a utilização do armazenamento, o desempenho do disco e previstos dias da capacidade de disco. Utilize a área de **utilização** para ver a utilização do espaço de disco na sua anfitriões de máquina virtual. Pode utilizar a área do **Desempenho do disco** para ver débito do disco e a latência no seu anfitriões de máquina virtual. Também pode utilizar a ferramenta de projecções para estimar quanto capacidade é esperada para estar disponível para um determinado intervalo de datas. Pode ver os detalhes sobre itens ligados ao clicar nelas.

Pode gerar um livro do Excel a partir desta informação capacidade para as seguintes categorias:

- Utilização do espaço em disco superior ao anfitrião
- Superior latência de em média por anfitrião

As seguintes áreas são apresentadas na página de **armazenamento** :

- *Utilização*: ver a utilização do espaço de disco no seu anfitriões de máquina virtual.
- *Espaço em disco total*: soma (espaço de disco lógico) para todos os anfitriões
- *Espaço em disco utilizado*: soma (espaço de disco lógico utilizados) para todos os anfitriões
- *Espaço disponível no disco*: espaço em disco menos espaço em disco utilizados Total
- *Percentagem disco utilizado*: utilizado espaço em disco dividido por espaço em disco total
- *Percentagem disco disponíveis*: espaço disponível no disco dividido por espaço em disco total

![imagem da página de capacidade gestão anexado armazenamento directo](./media/log-analytics-capacity/oms-capacity04.png)

**Desempenho do disco**

Ao utilizar OMS, pode ver a tendência históricas utilização do seu espaço de disco. A capacidade de projecções utiliza um algoritmo para utilização futura do projeto. Para utilização de espaço em particular, a funcionalidade de projecções permite-lhe quando poderá ser executado fora do espaço em disco do project. Isto irá ajudá-lo a planear armazenamento inicial e saber quando necessita de comprar mais armazenamento.

**Ferramenta de projecções**

Utilizando a ferramenta de projecções, pode ver tendências históricas para a utilização do espaço de disco. A capacidade de projecções também lhe permite quando estiver a executar o espaço no disco do project. Isto irá ajudá-lo a planear capacidade inicial e saber quando necessita de comprar mais capacidade de armazenamento.

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>Para trabalhar com itens na página direta armazenamento anexado

1. No dashboard de **Armazenamento anexados direta** , na área de **utilização** , pode ver as informações de utilização do disco.
2. Clique num item ligado para abri-lo na página de **pesquisa** e ver informações detalhadas acerca do mesmo.
3. Na área do **Desempenho do disco** , pode ver informações de débito e latência do disco.
4. Na **ferramenta de projecções**, mova o controlo de deslize de data para apresentar uma projecções de capacidade que será utilizada na data que escolher.


## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo Analytics](log-analytics-log-searches.md) para ver dados de gestão de capacidade detalhadas.
