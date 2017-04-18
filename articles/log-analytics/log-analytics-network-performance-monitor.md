<properties
    pageTitle="Solução de Monitor de desempenho no OMS de rede | Microsoft Azure"
    description="Ajuda a monitorizar o desempenho do seu redes-in perto real-time para detectar o Monitor de desempenho de rede e localize congestionamentos de desempenho de rede."
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
    ms.date="07/28/2016"
    ms.author="banders"/>

# <a name="network-performance-monitor-preview-solution-in-oms"></a>Solução de Monitor de desempenho (pré-visualização) no OMS de rede

>[AZURE.NOTE] Esta é uma [solução de pré-visualização](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Este documento descreve como configurar e utilizar a solução de Monitor de desempenho de rede no OMS, que ajuda a monitorizar o desempenho do seu redes-in perto real-time para Deteta e localize de rede congestionamentos de desempenho. Com a solução de Monitor de desempenho de rede, pode monitorizar a perda e latência entre duas redes, sub-redes ou servidores. Monitor de desempenho de rede detetar problemas de rede, como o tráfego blackholing, erros encaminhamento e problemas que não forem capazes de detetar métodos de monitorização de rede convencional. Monitor de desempenho de rede gera alertas e notifica-o como e quando for quebrado um limite de uma ligação de rede. Estes limiares podem aprendeu automaticamente pelo sistema ou pode configurá-los de utilizar regras de alertas personalizadas. Monitor de desempenho de rede garante a detecção atempada de problemas de desempenho de rede e localizes a origem do problema a um determinado segmento de rede ou dispositivo.

Pode detetar problemas de rede com o dashboard de solução que apresenta informações resumidas sobre a sua rede, incluindo recentes eventos de estado de funcionamento da rede, ligações de rede danificado e ligações de sub-rede que são confrontadas com perda de pacotes alta e a latência. É possível desagregar uma ligação de rede para ver o estado de funcionamento atual de ligações de sub-rede bem como ligações nó a nó. Também pode ver a tendência histórica de perda e latência à rede, sub-rede e nó a nó nível. Pode detetar problemas de rede breves visualizando gráficos históricas tendência para perda de pacotes e a latência e localize congestionamentos na rede num mapa da topologia. Gráfico de topologia interativos permite-lhe visualizar as rotas da rede de salto por salto e determinar a origem do problema. Como outras soluções, pode utilizar o registo de pesquisa para diferentes requisitos de análise para criar relatórios personalizados com base nos dados recolhidos pelo Monitor de desempenho de rede.

A solução utiliza transações síntese como um mecanismo principal para detetar falhas de rede. Sim, pode utilizá-lo sem ter em consideração fornecedor ou modelo de um dispositivo de rede específica. Funciona através de ambientes híbridos, nuvem (IaaS) e no local. A solução Deteta automaticamente a topologia da rede e várias vias na sua rede.

Produtos de monitorização de rede típicas concentrar-se sobre como monitorizar o estado de funcionamento do dispositivo (routers, parâmetros, etc.) de rede, mas não fornecem informações para a qualidade real da conectividade de rede entre dois pontos, que é que o Monitor de desempenho de rede.

### <a name="using-the-solution-standalone"></a>Utilizar o solução autónomo

Se pretender controlar a qualidade das ligações de rede entre os respetivos críticas das cargas de trabalho, redes, centros de dados ou sites do office, em seguida, pode utilizar a solução de Monitor de desempenho de rede por si só para monitorizar o estado de funcionamento de conectividade entre:

- vários sites centros de dados ou o office, que estão ligados a utilizar uma rede pública ou privada
- críticas cargas de trabalho que estão a executar o linha de aplicações empresariais
- como o serviços em nuvem público Microsoft Azure ou Amazon Web Services (AWS) e redes no local, se tiver IaaS (VM) disponíveis e tiver gateways configurados para permitir a comunicação entre redes de no local e redes de nuvem
- Redes Azure e no local ao utilizar o encaminhamento de Express

### <a name="using-the-solution-with-other-networking-tools"></a>Utilizar a solução com outras ferramentas de rede

Se pretender monitorizar a uma linha de aplicação empresarial, pode utilizar a solução de Monitor de desempenho de rede como uma solução acompanha para outras ferramentas de rede. Uma rede lenta pode levar a aplicações lentas e Monitor de desempenho de rede pode ajudar a investigar problemas de desempenho de aplicação que são causados por problemas de rede subjacentes. Uma vez que a solução não requer qualquer acesso a dispositivos de rede, o administrador da aplicação não necessite de depender de uma equipa de rede para fornecer informações sobre como a rede está a afectar aplicações.

Além disso, se já investir em outras ferramentas de monitorização de rede, em seguida, a solução pode complementar essas ferramentas porque mais tradicionais soluções de monitorização de rede não fornecem informações para métricas de desempenho de rede ponto a ponto como perda e latência.  A solução de Monitor de desempenho de rede pode ajudar a preencher nesse intervalo.


## <a name="installing-and-configuring-agents-for-the-solution"></a>Instalar e configurar agentes de para a solução

Utilize os processos básicos para instalar agentes em [computadores com o Windows ligar-se para a análise de registo](log-analytics-windows-agents.md) e [Ligar o Gestor de operações para a análise de registo](log-analytics-om-agents.md).

>[AZURE.NOTE]
Terá de instalar, pelo menos, 2 agentes para poder possui dados suficientes para detetar e monitorizar os recursos da rede. Caso contrário, a solução permanecerá num Estado configurar até instalar e configurar agentes de adicionais.

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes

Antes de instalar agentes, considere a topologia da rede e quais as partes da rede que pretende monitorizar. Recomendamos que instale mais do que um agente para cada sub-rede que pretende monitorizar. Por outras palavras, para cada sub-rede que pretende monitorizar, selecione duas ou mais servidores ou VMs e instalar o agente nos mesmos.

Se tem a certeza sobre a topologia da sua rede, instale os agentes em servidores com críticos das cargas de trabalho para monitorizar o desempenho da rede onde pretende. Por exemplo, poderá pretender manter um registo de uma ligação de rede entre um servidor Web e servidor a executar o SQL Server. Neste exemplo, terá de instalar um agente em ambos os servidores.

Agentes monitorizam a conectividade da rede (ligações) entre anfitriões – não os anfitriões próprios. Sim, para monitorizar uma ligação de rede, tem de instalar agentes em ambos os pontos finais dessa ligação.

### <a name="configure-agents"></a>Configurar agentes de

Após instalar agentes, terá de abrir as portas da firewall para outros computadores para se certificar de que podem comunicar agentes. Tem de transferir e, em seguida, execute o [script EnableRules.ps1 PowerShell](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) sem quaisquer parâmetros numa janela do PowerShell com privilégios administrativos

O script cria chaves de registo necessárias o Monitor de desempenho de rede e que cria as regras da firewall do Windows para permitir que agentes criar ligações de TCP com os outros. As chaves de registo criadas pelo script também especifique se pretende iniciar os registos de depuração e o caminho para o ficheiro de registos. Também define a porta TCP agente utilizada para a comunicação. Os valores para estas teclas são automaticamente definidos por um script, para que não deve alterar manualmente estas teclas.

A porta aberta por predefinição é 8084. Pode utilizar uma porta personalizada ao fornecer o parâmetro `portNumber` para o script. No entanto, a mesma porta deve ser utilizada em todos os computadores onde o script é executado.

>[AZURE.NOTE] O script EnableRules.ps1 configura as regras de firewall do Windows apenas no computador onde o script é executado. Se tiver uma firewall de rede, deve certificar-se de que permitir o tráfego destinado a porta TCP a ser utilizada pelo Monitor de desempenho de rede.


## <a name="configuring-the-solution"></a>Configurar a solução

Utilize as seguintes informações para instalar e configurar a solução.

1. A solução de Monitor de desempenho de rede adquire dados a partir de computadores que executam o Windows Server 2008 SP 1 ou posterior ou o Windows 7 SP1 ou posterior, quais são os requisitos do mesmo como o Microsoft monitorização agente MMA ().
2. Adicione a solução de Monitor de desempenho de rede à área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  
  ![Símbolo de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. No portal do OMS, verá um novo mosaico intitulado **Monitor de desempenho de rede** com a mensagem *solução exige configuração adicional*. Terá de configurar a solução para adicionar redes com base em sub-redes e nós que sejam detetados por agentes. Clique em **Monitor de desempenho de rede** para começar a configurar a rede predefinida.  
  ![solução exige configuração adicional](./media/log-analytics-network-performance-monitor/npm-config.png)


### <a name="configure-the-solution-with-a-default-network"></a>Configurar a solução com uma rede predefinida

Na página configuração, verá uma única rede com o nome **predefinido**. Quando ainda não tiver definido as redes, todas as sub-redes automaticamente descobertas são colocadas na rede predefinido.

Sempre que cria uma rede, adicionar uma sub-rede à mesma, e essa sub-rede é removida da rede predefinida. Se eliminar uma rede, todas as suas sub-redes são devolvidas automaticamente para a rede predefinida.

Por outras palavras, a rede predefinida é o contentor para todas as sub-redes que não estão contidos em qualquer rede definidos pelo utilizador. Não pode editar ou eliminar a rede predefinida. Sempre permanece no sistema. No entanto, pode criar redes tantas que precisar.

Na maioria dos casos, as sub-redes na sua organização serão ser ordenadas por mais do que uma rede e deve criar uma ou mais redes para agrupar as sub-redes logicamente.

### <a name="create-new-networks"></a>Criar novas redes

Uma rede no Monitor de desempenho de rede é um contentor para sub-redes. Pode criar uma rede com qualquer nome que pretende e adicionar sub-redes à rede. Por exemplo, pode criar uma rede com o nome *Building1* e, em seguida, adicionar sub-redes ou pode criar uma rede com o nome *DMZ* e, em seguida, adicionar todas as sub-redes que pertencem a zona para esta rede.

#### <a name="to-create-a-new-network"></a>Para criar uma nova rede

1. Clique em **Adicionar a rede** e, em seguida, escreva o nome de rede e a descrição.
2.  Selecione uma ou mais sub-redes e, em seguida, clique em **Adicionar**.
3. Clique em **Guardar** para guardar a configuração.  
  ![Adicionar rede](./media/log-analytics-network-performance-monitor/npm-add-network.png)



### <a name="wait-for-data-aggregation"></a>Aguarde que a agregação de dados

Se já tiver guardado a configuração pela primeira vez, a solução é iniciado recolher informações de perda e latência do pacote de rede entre os nós onde agentes estão instalados. Este processo pode demorar algum tempo, por vezes, através de 30 minutos. Durante este estado, o mosaico de Monitor de desempenho de rede na página Descrição geral do apresenta uma mensagem a indicar *a agregação de dados no processo*.

![agregação de dados em curso](./media/log-analytics-network-performance-monitor/npm-aggregation.png)


Quando os dados foi carregados, verá os dados a mostrar o mosaico atualizado de Monitor de desempenho de rede.

![Mosaico de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-tile.png)

Clique no mosaico para visualizar o dashboard de Monitor de desempenho de rede.

![Dashboard de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Editar definições de monitorização para sub-redes

São apresentadas todas as sub-redes onde foi instalado, pelo menos, um agente do separador **sub-redes** a página de configuração.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Para ativar ou desativar a monitorização de sub-redes específicos

1. Selecione ou desmarque a caixa junto a **ID de sub-rede** e, em seguida, certifique-se de que a **utilização de monitorização** é selecionado ou desmarcada, conforme adequado. Pode selecionar ou desmarcar várias sub-redes. Quando desativado, sub-redes não são monitorizadas, tal como os agentes serão atualizados para parar o ping outros agentes.
2. Selecione os nós que pretende para monitorizar a para uma determinada sub-rede ao selecionar sub-rede a partir da lista e mover os nós necessários entre as listas que contêm nós não monitorizadas e monitorizadas.
Pode adicionar uma **Descrição** de personalizado para sub-rede, se quiser.
3. Clique em **Guardar** para guardar a configuração.  
  ![Editar sub-rede](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Selecione nós para monitorizar a

Todos os nós que possui um agent instalado nos mesmos são listados no separador **nós** .

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Para ativar ou desativar a monitorização de nós

1. Selecione ou desmarque os nós que pretende monitorizar ou parar a monitorização.
2. Clique em **usar para monitorizar**ou desmarcá-la, conforme adequado.
3. Clique em **Guardar**.  
  ![Ativar a monitorização de nó](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)


### <a name="set-monitoring-rules"></a>Conjunto de regras de monitorização

Monitor de desempenho de rede gera eventos de estado de funcionamento sobre a ligação entre um par de nós ou ligações de rede ou sub-rede quando um determinado limiar for quebrado. Estes limiares podem aprendeu automaticamente pelo sistema ou pode configurá-los regras alertas personalizadas.

A *regra predefinida* é criado pelo sistema e cria um evento de estado de funcionamento sempre perda ou latência entre cada par de redes ou sub-rede ligações violações o limiar aprendeu de sistema. Pode escolher desativar a regra predefinida e criar regras de monitorização personalizadas

#### <a name="to-create-custom-monitoring-rules"></a>Para criar regras de monitorização personalizadas

1. Clique em **Adicionar regra** no separador **Monitor** e introduza o nome da regra e a descrição.
2. Selecione o par das ligações de rede ou sub-rede para monitorizar a partir das listas.
3. Em primeiro lugar selecione à rede no qual está contido sub-rede/s primeiro de interesse na lista pendente de rede e, em seguida, selecione sub-rede/s do menu pendente de sub-rede correspondente.
Selecione **todas as sub-redes** se pretender monitorizar todas as sub-redes uma ligação de rede. Selecione da mesma forma a outros sub-rede/s de interesse. E, pode clicar em **Adicionar exceção** para excluir a monitorização de ligações de sub-rede específico a partir da seleção que efetuou.
4. Se não quiser criar eventos de estado de funcionamento para os itens que selecionou, em seguida, desmarque **Ativar o estado de funcionamento de monitorização nas hiperligações tapadas por esta regra**.
5. Escolha condições de monitorização.
Pode definir limiares personalizados para a criação de evento de estado de funcionamento ao escrever os valores do limiar. Sempre que o valor da condição vai acima respectivo limiar selecionado para o par de rede/sub-rede selecionada, é gerado um evento de estado de funcionamento.
6. Clique em **Guardar** para guardar a configuração.  
  ![Criar regra monitorização personalizada](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)


## <a name="data-collection-details"></a>Detalhes de recolha de dados

Monitor de desempenho de rede utiliza pacotes de handshake SYNACK-TCP SYN-ACK para recolher perda e informações de latência e rastreio de rota também é utilizado para obter informações de topologia.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para Monitor de desempenho de rede.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
| Windows |![Sim](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![Sim](./media/log-analytics-network-performance-monitor/oms-bullet-green.png)|![N](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|            ![N](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)|![N](./media/log-analytics-network-performance-monitor/oms-bullet-red.png)| TCP handshakes 5 segundos, dados de cada 3 minutos enviadas |

A solução torna a utilização das transações síntese para avaliar o estado de funcionamento da rede. Agentes OMS instalados em vários ponto os rede exchange os pacotes TCP com um do outro e, no processo, saiba a perda de tempo e o pacote de ida e volta, se existirem. Periodicamente, cada agente também executa uma análise da rota com outros agentes para localizar todas as várias rotas na rede que deve ser testada. Utilizar estes dados, os agentes conseguem deduzir a latência da rede e ilustrações de perda de pacotes. Os testes são repetidos em cinco segundos e dados são agregados pelos agentes durante um período de três minutos antes de carregá-lo para OMS.

>[AZURE.NOTE] Apesar de agentes de comunicarem com os outros frequentemente, estes não geram muitas tráfego de rede durante a realização os testes. Agentes dependem apenas em pacotes de handshake SYNACK-TCP SYN-ACK para determinar a perda e latência – sem dados são trocados pacotes. Durante este processo, agentes de comunicarem com os outros apenas quando for necessário e a topologia de comunicação agente está optimizada para reduzir o tráfego de rede.

## <a name="using-the-solution"></a>Utilizar a solução

Esta secção explica dashboard de funções e como utilizá-las.

### <a name="solution-overview-tile"></a>Mosaico de descrição geral da solução

Depois de ter ativado a solução de Monitor de desempenho de rede, o mosaico de solução na página Descrição geral de OMS fornece uma descrição geral do Estado de funcionamento de rede. Apresenta um gráfico em anel a mostrar o número de ligações de sub-rede saudável e danificado. Quando clicar no título, é aberto o dashboard de solução.

![Mosaico de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-tile.png)


### <a name="network-performance-monitor-solution-dashboard"></a>Dashboard de solução de Monitor de desempenho de rede

O **Resumo de rede** pá apresenta um resumo das redes juntamente com o seu tamanho relativa. Isto é seguido mosaicos a mostrar o número total de ligações de rede, ligações de sub-rede e caminhos no sistema de (um caminho consiste nos endereços IP dos dois anfitriões com agentes e todos os saltos entre eles).

O pá **Eventos de estado de funcionamento da rede de início** fornece uma lista de eventos do mais recentes do Estado de funcionamento e alertas no sistema e a hora de uma vez que o evento foi ativo. Um evento de estado de funcionamento ou um alerta é gerada sempre que a perda de pacotes ou uma latência de uma ligação de rede ou sub-rede excede um determinado limiar.

A **Ligações de rede danificado início** pá mostra uma lista de ligações de rede danificado. Estas são as ligações de rede que tenham um ou mais evento de estado de funcionamento adverso das mesmas no momento.

As lâminas **Início sub-rede ligações com mais de perda** e **Ligações de sub-rede com mais de latência** mostram as ligações de sub-rede superior ao perda de pacotes e ligações de sub-rede superior por latência respetivamente. Latência alta ou algumas quantidade de perda de pacotes poderá ser esperada em determinados ligações de rede. Essas ligações aparecem nas dez listas principais mas não são marcadas danificadas.

A **Consultas comuns** pá contém um conjunto de consultas de pesquisa que obter rede não processada monitorizar os dados diretamente. Pode utilizar estas consultas como ponto de partida para criar o seus próprio consultas para elaboração de relatórios personalizados.

![Dashboard de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-dash01.png)


### <a name="drill-down-for-depth"></a>Desagregação para profundidade

Pode clicar em várias ligações na solução dashboard para desagregação mais aprofundada em qualquer área de interesse. Por exemplo, quando vê um alerta ou aparecem no dashboard de uma ligação de rede danificado, pode clicar para investigar ainda mais. Que vai ser feito para uma página que lista todas as ligações de sub-rede para a ligação de rede em particular. Poderá ver o estado de perda, latência e estado de funcionamento de cada ligação de sub-rede e rapidamente a descobrir que ligações de sub-rede estão a causar o problema. Em seguida, pode clicar em **hiperligações de nó de vista** para ver todas as hiperligações de nó para a ligação de sub-rede danificado. Em seguida, pode ver as ligações de nó a nó individuais e encontrar as ligações de nó danificado.

Pode clicar em **topologia de vista** para ver a topologia de salto por salto de rotas entre os nós de origem e destino. O rotas danificadas ou saltos são apresentados em vermelho para que possam identificar rapidamente o problema de uma determinada parte da rede.

![dados de desagregação](./media/log-analytics-network-performance-monitor/npm-drill.png)


#### <a name="trend-charts"></a>Gráficos de tendência

Em cada nível que desagregação, pode ver a tendência de perda e latência para uma ligação de rede. Gráficos de tendência também estão disponíveis para ligações de sub-rede e nó. Pode alterar o intervalo de tempo no gráfico para desenhar ao utilizar o controlo de tempo na parte superior do gráfico.

Gráficos de tendência mostram-lhe uma perspetiva histórica do desempenho de uma ligação de rede. Alguns problemas de rede estão breves natureza e seriam difíceis de capturas apenas verificando o estado atual da rede. Isto acontece porque problemas podem revelar rapidamente e desaparecer antes de qualquer pessoa avisos, apenas para voltar a surgir num ponto posterior altura. Estes problemas breves também podem ser difícil para administradores de aplicação de uma vez que os problemas com frequência superfície como aumentos inexplicados de tempo de resposta de aplicação, mesmo quando aparecem todos os componentes de aplicação executar sem problemas.

Pode facilmente Deteta esses tipos de problemas verificando um gráfico de tendência onde o problema aparecerá como uma coletor repentina sobre latência da rede ou perda de pacotes.

![gráfico de tendência](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Mapa da topologia de salto por salto

Monitor de desempenho de rede mostra-lhe a topologia de salto por salto de rotas entre dois nós de um mapa interativo da topologia. Pode ver o mapa de topologia ao selecionar uma ligação de nó e, em seguida, clicando em **topologia da vista**. Além disso, pode ver o mapa de topologia ao clicar em mosaico **caminhos** no dashboard. Quando clica em **caminhos** no dashboard de, terá de selecionar os nós de origem e de destino a partir do painel esquerdo e, em seguida, clique em **desenho** para desenhar rotas entre dois nós.

O mapa de topologia apresenta rotas quantos sejam encontrarem entre dois nós e o que caminhos levar os pacotes de dados. Congestionamentos de desempenho de rede são marcados vermelho no mapa da topologia. Pode localizar uma ligação de rede danificada ou um dispositivo de rede cablagem verificando elementos coloridos vermelhos no mapa da topologia.

Quando clica num nó ou ao pairar sobre a mesma no mapa topologia, irá ver as propriedades do nó como FQDN e o endereço IP. Clique numa hop para ver é endereço IP. Pode realçar rotas específicas ao limpar e, em seguida, selecionar apenas as rotas que pretende realçar no mapa. Pode ampliar ou reduzir o mapa de topologia utilizando a roda do rato.

Note que a topologia apresentada no mapa é topologia de camada 3 e não contém ligações e dispositivos de camada 2.

![mapa da topologia de salto por salto](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Localização de falhas

Monitor de desempenho de rede é consiga encontrar os congestionamentos na rede sem ser necessário ligar os dispositivos de rede. Basear os dados que reúne da rede e aplicando algoritmos avançados no gráfico de rede, Monitor de desempenho de rede faz com que uma estimativa probabilística das partes da rede que estão mais predispostos a origem do problema.

Esta abordagem é útil para determinar os congestionamentos na rede quando acesso ao saltos não está disponível porque não necessite de quaisquer dados para recolhidas a partir de dispositivos de rede como routers ou parâmetros. Isto também é útil quando os saltos entre dois nós não estiverem na sua controlo administrativo. Por exemplo, os saltos poderá ISP routers.

### <a name="log-analytics-search"></a>Pesquisa de análise de registo

Todos os dados que são páginas graficamente expostas através do dashboard de Monitor de desempenho de rede e desagregação também está disponível vierem na pesquisa de análise de registo. Pode consultar os dados com a linguagem de consulta de pesquisa e criar relatórios personalizados, os dados a exportar para o Excel ou obter. O pá **Consultas comuns** no dashboard tem algumas útil consultas que pode utilizar como ponto de partida para criar os seus próprios relatórios e consultas.

![consultas de pesquisa](./media/log-analytics-network-performance-monitor/npm-queries.png)


## <a name="investigate-the-root-cause-of-a-health-alert"></a>Investigar a causa de raiz de um alerta de estado de funcionamento

Agora que já ler sobre o Monitor de desempenho de rede, vamos olhar um inquérito simple para a causa de raiz para um evento de estado de funcionamento.

1. Na página Descrição geral, irá obter um instantâneo rápido do Estado de funcionamento da sua rede ao observar o **Monitor de desempenho de rede** dispor em mosaico. Repare que terminar as ligações de 80 sub-redes a ser monitorizadas, 43 são danificado. Isto merece o inquérito. Clique no mosaico para visualizar o dashboard de solução.  
  ![Mosaico de Monitor de desempenho de rede](./media/log-analytics-network-performance-monitor/npm-investigation01.png)

2. A imagem de exemplo abaixo, notará que estão atualmente 4 eventos de estado de funcionamento e 4 ligações de rede que são danificadas. Decidir investigar o problema e clique na ligação de rede **Web do Sharepoint** para saber a raiz do problema.  
  ![exemplo de ligação de rede danificado](./media/log-analytics-network-performance-monitor/npm-investigation02.png)

3. A página de desagregação mostra todas as ligações de sub-rede na ligação de rede de **Web do Sharepoint** . Notará que para ambas as ligações de sub-rede, a latência tem cruzados o limiar de efetuar a ligação de rede danificado. Também pode ver as tendências de latência das ligações de sub-rede. Pode utilizar a seleção de tempo controlo no gráfico para focar o intervalo de tempo necessários. Pode ver a hora do dia quando latência atingiu o pico. Pode procurar mais tarde os registos para este período de tempo investigar o problema. Clique em **ligações de nó de vista** para desagregação ainda mais.  
  ![exemplo de ligações de sub-rede danificado](./media/log-analytics-network-performance-monitor/npm-investigation03.png)

4.  Semelhante à página anterior, a página de desagregação da ligação de sub-rede específico de lista para baixo suas ligações de nó constituintes. Pode executar ações semelhantes aqui, como fez no passo anterior. Clique em **topologia de vista** para ver a topologia de entre os nós de 2.  
  ![exemplo de ligações de nó danificado](./media/log-analytics-network-performance-monitor/npm-investigation04.png)

5. Todos os caminhos entre os nós seleccionados 2 são representados num mapa da topologia. Pode visualizar a topologia de salto por salto de rotas entre dois nós no mapa da topologia. Dá-lhe uma visão clara do existem rotas quantos entre dois nós e o que caminhos os pacotes de dados estão a efetuar. Congestionamentos de desempenho de rede são marcados cores vermelho. Pode localizar uma ligação de rede danificada ou um dispositivo de rede cablagem verificando elementos coloridos vermelhos no mapa da topologia.  
  ![exemplo da vista de topologia danificado](./media/log-analytics-network-performance-monitor/npm-investigation05.png)

6. A perda, latência e o número de saltos em cada path podem ser revistos no painel de **Detalhes do caminho** . Neste exemplo, pode ver que não existem 3 caminhos danificados tal como mencionado no painel de. Utilize a barra de deslocamento para ver os detalhes desses caminhos danificados.  Utilize as caixas de verificação para selecionar uma das formas de modo a que está desenhada a topologia de caminho apenas uma. Pode utilizar a roda do rato para ampliar ou reduzir o mapa de topologia.

  No abaixo da imagem pode ver claramente o cabo das áreas problema para a secção específica da rede verificando os caminhos e os saltos na cor vermelha. Se clicar num nó no mapa de topologia, são as propriedades de nó, incluindo o FQDN e o endereço IP. Clicar numa hop mostra o endereço IP do salto.  
  ![topologia danificada - caminho detalhes de exemplo](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="next-steps"></a>Próximos passos

- [Registos de pesquisa](log-analytics-log-searches.md) para ver registos de dados de desempenho de rede detalhado.
