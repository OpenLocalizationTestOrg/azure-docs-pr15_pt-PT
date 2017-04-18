<properties
    pageTitle="Analisar o desempenho do limite no Azure CDN | Microsoft Azure"
    description="Analise desempenho de nós margem no Microsoft Azure CDN. Análise de desempenho de limite fornece informações granular tráfego e largura de banda de utilização para a CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Analisar desempenho de nós margem no Microsoft Azure CDN

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Análise de desempenho de limite fornece informações granular tráfego e largura de banda de utilização para a CDN. Estas informações, em seguida, podem ser utilizadas para gerar as estatísticas de tendência, que permitem-lhe ganhar conhecimentos aprofundados no modo como os seus ativos estão a ser em cache e entregue para os seus clientes. Por sua vez, esta opção permite-lhe uma estratégia de formulário no como otimizar a entrega do seu conteúdo e determinar que devem ter problemas abordados para aproveitar melhor a CDN. Como resultado, não só será que poderá melhorar o desempenho de entrega de dados, mas também poderá reduzir os custos CDN.

> [AZURE.NOTE] Todos os relatórios utilizam notação UTC/GMT quando especificar uma data/hora.

## <a name="reports-and-log-collection"></a>Relatórios e colecção de registos

Dados de atividade CDN devem ser recolhidos pelo módulo do limite desempenho Analytics antes que pode gerar relatórios no mesmo. Este processo de coleções de sites ocorre uma vez por dia e -aborda a atividade que substituiu durante o dia anterior. Isto significa que as estatísticas de um relatório representam uma amostra da estatísticas do dia ao tempo foi processada e para que não seja necessariamente contêm o conjunto completo de dados do dia atual. A função principal destes relatórios é avaliar o desempenho. Não deve ser utilizadas para fins de faturaçãohttps ou exatas estatísticas numéricas.

> [AZURE.NOTE] Os dados não processados a partir do qual relatórios limite desempenho analítico são gerados estão disponíveis pelo menos cerca de 90 dias.

## <a name="dashboard"></a>Dashboard

Dashboard de análise de desempenho de limite controla o tráfego CDN atual e histórico através de um gráfico e estatísticas. Utilize este dashboard para detetar tendências recentes e a longo prazo sobre o desempenho de tráfego CDN para a sua conta.

Este dashboard consiste em:

* Um gráfico interativo que lhe permite a visualização dos métricas chave e tendências.
* Uma linha de tempo que fornece uma noção de padrões de longo prazo métricas chave e tendências.
* Métricas chave e estatísticas informações sobre como nossa rede CDN melhora o tráfego do site medido por desempenho geral, a utilização e eficiência.

### <a name="accessing-the-edge-performance-dashboard"></a>Aceder ao dashboard de desempenho de margem

1. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

2. Paire sobre o separador de **análise** , em seguida, coloque o cursor sobre a lista de opções de **Análise de desempenho do limite** .  Clique no **Dashboard**.

    É apresentado no dashboard de análise de nó do limite.

### <a name="chart"></a>Gráfico

O dashboard contém um gráfico que controla uma métrica período de tempo selecionado na linha de tempo que aparece diretamente abaixo do mesmo.  É apresentada uma linha cronológica que gráficos para cima até à última dois anos da atividade CDN diretamente abaixo do gráfico.

#### <a name="using-the-chart"></a>Utilizar o gráfico

* Por predefinição, a taxa de eficiência da cache de últimos 30 dias vai ser colocados no mesmo.
* Este gráfico é gerado a partir de dados agrupados diariamente.
* Pairar o rato sobre um dia no gráfico de linha irá indicar uma data e o valor da métrica nessa data.
* Clique em realçar fins de semana para ativar/desativar uma sobreposição da simplificada barras verticais a cinzento que representam os fins de semana para o gráfico. Este tipo de sobreposição é útil para identificar padrões de tráfego sobre os fins de semana.
* Clique em vista de um ano há para ativar/desativar uma sobreposição da atividade no ano anterior ao longo do mesmo período de tempo para o gráfico. Este tipo de comparação fornece visão padrões de utilização CDN a longo prazo. No canto de mão do canto superior direito do gráfico contém uma legenda que indica o código de cores para cada gráfico de linha.

#### <a name="updating-the-chart"></a>Actualizar o gráfico

* Intervalo de tempo: Execute um dos seguintes procedimentos:
    * Selecione a região pretendida na linha cronológica. O gráfico será atualizado com os dados que corresponde ao período de tempo selecionado.
    * Faça duplo clique no gráfico para apresentar todos os dados de históricos disponíveis até um máximo de dois anos.
* Métrica: Clique no ícone de gráfico que aparece junto a métrica do pretendido. O gráfico e a linha cronológica vai ser atualizados com os dados para a métrica do correspondente.


### <a name="key-metrics-and-statistics"></a>Métricas chave e estatísticas

#### <a name="efficiency-metrics"></a>Métricas de eficiência

É o objetivo nestas métricas ver se a eficiência de cache pode ser melhorada. Os benefícios principais derivados de eficiência de cache são:

* Carga reduzida no servidor de origem que pode conduzir a:
    * Melhor desempenho do servidor web.
    * Reduzir custos operacionais.
* Melhorado aceleração de entrega de dados de uma vez que serão servidos mais pedidos diretamente a partir da CDN.

Campo | Descrição
------|------------
Eficiência de cache | Indica a percentagem de dados transferidos que foram servidos da cache. Este medidas métrica quando existe uma versão em cache do conteúdo pedida foi servido diretamente a partir da CDN (servidores edge) ao solicitadores (por exemplo, browser web)
Taxa de acertos | Indica a percentagem de pedidos de que foram servida da cache. Este medidas métrica quando existe uma versão em cache do conteúdo pedida foi servido diretamente a partir da CDN (servidores edge) ao solicitadores (por exemplo, browser web).
% de Bytes remotos - sem Config Cache | Indica a percentagem de tráfego que foi servido de servidores de origem para CDN (servidores edge) que não será possível em cache estatístico como resultado da funcionalidade de Cache de ignorar (motor de regras de HTTP).
% de Bytes remotos - Cache expirado | Indica a percentagem de tráfego que foi servido de servidores de origem para a CDN (servidores edge) como resultado obsoleta revalidação conteúda.

#### <a name="usage-metrics"></a>Métricas de utilização

É o objetivo nestas métricas fornecer visão as medidas de custo corte seguintes:

* Minimizar custos operacionais através de CDN.
* Reduzir as despesas CDN através de compressão e eficiência da cache.

> [AZURE.NOTE] Números de volume de tráfego representam tráfego que foi utilizado nos cálculos de relações e percentagens e poderá mostrar apenas uma parte do tráfego total para os clientes de volume de alta.

Campo | Descrição
------|------------
Guardar Bytes saída | Indica o número médio de bytes transferidos para cada pedido servido a partir da CDN (servidores edge) para o autor da requisição (por exemplo, browser web).
Taxa de Config Byte sem Cache | Indica a percentagem de tráfego servida a partir da CDN (servidores edge) para o autor da requisição (por exemplo, browser web) que não será possível em cache devido a funcionalidade de Cache de ignorar.
Taxa de Byte comprimido | Indica a percentagem de tráfego enviado a partir de CDN (servidores edge) para solicitadores (por exemplo, browser web) num formato comprimido.
Bytes saída | Indica a quantidade de dados, em bytes, que foram entregues a partir de CDN (servidores edge) para o autor da requisição (por exemplo, browser web).  
Bytes em | Indica a quantidade de dados, em bytes, enviados a partir do solicitadores (por exemplo, browser web) para a CDN (servidores edge).
Bytes remoto | Indica a quantidade de dados, em bytes, enviados a partir do cliente e CDN servidores de origem para a CDN (servidores edge).

#### <a name="performance-metrics"></a>Métricas de desempenho

É o objetivo nestas métricas controlar o desempenho geral de CDN para o tráfego da sua.

Campo | Descrição
------|------------
Velocidade de transferência | Indica a velocidade média em que foi transferido conteúdo a partir da CDN para um autor da requisição.
Duração | Indica o tempo médio, em milissegundos, demorou a um ativo para um autor da requisição (por exemplo, browser web).
Taxa de pedido de comprimido | Indica a percentagem de acertos que foram entregues a partir de CDN (servidores edge) para o autor da requisição (por exemplo, browser web) num formato comprimido.
Taxa de erros de 4xx | Indica a percentagem de acertos gerados por um código de estado 4xx.
Taxa de erros de 5XX | Indica a percentagem de acertos gerados por um código de estado 5xx.
Acertos | Indica o número de pedidos de CDN conteúdo.

#### <a name="secure-traffic-metrics"></a>Proteger o tráfego métricas

É o objetivo nestas métricas controlar o desempenho de CDN para o tráfego de HTTPS.

Campo | Descrição
------|------------
Proteger a Cache eficiência | Indica a percentagem de dados transferidos para pedidos de HTTPS que vieram da cache. Este medidas métrica quando existe uma versão em cache do conteúdo pedida foi servido diretamente a partir da CDN (servidores edge) solicitadores (por exemplo, browser web) por HTTPS.
Proteger a velocidade de transferência | Indica a velocidade média em que foi transferido conteúdo a partir da CDN (servidores edge) para solicitadores (por exemplo, servidores web) por HTTPS.
Duração segura média | Indica o tempo médio, em milissegundos, demorou a um ativo para um autor da requisição (por exemplo, browser web) por HTTPS.
Acertos seguros | Indica o número de pedidos de HTTPS para conteúdo CDN.
Seguro Bytes saída | Indica a quantidade de tráfego HTTPS, em bytes, que foram entregues a partir de CDN (servidores edge) para o autor da requisição (por exemplo, browser web).

## <a name="reports"></a>Relatórios

Cada relatório neste módulo contém um gráfico e estatísticas sobre a utilização da largura de banda e o tráfego para diferentes tipos de métricas (por exemplo, códigos de estado HTTP, códigos de estado da cache, pedido do URL, etc.). Estas informações podem ser utilizadas para o delve mais aprofundada para como o conteúdo está a ser servido para os seus clientes e para ajustar o comportamento CDN para melhorar o desempenho de entrega de dados.

### <a name="accessing-the-edge-performance-reports"></a>Aceder a relatórios de desempenho de margem

1. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-edge-performance/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

2. Paire sobre o separador de **análise** , em seguida, coloque o cursor sobre a lista de opções de **Análise de desempenho do limite** .  Clique no **objeto grande HTTP**.

    É apresentado no ecrã de relatórios de análise de nó limite.

Relatório | Descrição
-------|------------
Resumo diário | Permite-lhe ver tendências de tráfego diária durante um período de tempo especificado. Cada barra neste gráfico representa uma data específica. O tamanho da barra de indica o número total de acertos que ocorreram dessa data.
Resumo do preço por hora | Permite-lhe ver tendências de tráfego de hora a hora durante um período de tempo especificado. Cada barra neste gráfico representa uma única hora numa data específica. O tamanho da barra de indica o número total de acertos que ocorreram durante a essa hora.
Protocolos | Apresenta a desagregação de tráfego entre os protocolos HTTP e HTTPS. Um gráfico em anel indica a percentagem de acertos que ocorreram para cada tipo de protocolo.
Métodos HTTP | Permite-lhe uma ideia rápida das qual HTTP métodos estão a ser utilizados para pedir os seus dados. Normalmente, os métodos de pedido HTTP mais comuns são GET, HEAD e publicar. Um gráfico em anel indica a percentagem de acertos que ocorreram para cada tipo de método de pedido HTTP.
URLs | Contém um gráfico que apresenta os URLs pedidos 10 superiores. É apresentada uma barra para cada URL. A altura da barra de indica quantos acertos gerados pelo URL específico ao longo do intervalo de tempo abrangido pelo relatório. Estatísticas para o topo 100 pedida que URLs são apresentados diretamente abaixo este gráfico.
CNAMEs | Contém um gráfico que apresenta a parte superior de um relatório do intervalo de 10 CNAMEs utilizadas para pedir a elementos ao longo do tempo. Estatísticas para o topo 100 pedida que CNAMEs são apresentados diretamente abaixo este gráfico.
Origens diferentes | Contém um gráfico que apresenta a CDN 10 superior ou os servidores de origem do cliente a partir do qual foram pedidos activos durante um período de tempo especificado. Estatísticas para o topo 100 pedida são apresentados os servidores de origem de cliente ou de CDN diretamente abaixo este gráfico. Os servidores de origem do cliente são identificados por nome definido a opção de nome de diretório.
POP geo | Mostra a quantidade de tráfego da sua está a ser encaminhado através de uma determinada ponto-de-presença (POP). A abreviatura três letras representa um POP na nossa rede CDN.
Clientes | Contém um gráfico que apresenta os clientes de 10 principais que pediram activos durante um período de tempo especificado. Para efeitos deste relatório, todos os pedidos provêm o mesmo endereço IP são considerados a partir do cliente do mesmo. São apresentadas as estatísticas para os clientes dos 100 valores superiores diretamente abaixo este gráfico. Este relatório é útil para determinar padrões de atividade de transferência para os seus clientes superiores.
Estados de cache | Fornece uma detalhadas análise do comportamento de cache, que pode revelar abordagens para melhorar a experiência de utilizador final global. Uma vez que o desempenho mais rápido provêm de acertos na cache, pode otimizar velocidades de entrega de dados ao redução falhas na cache e acertos expirados.
NENHUM detalhes | Contém um gráfico que apresenta os 10 URLs superiores para recursos para o qual a última modificação de conteúdo do cache não foi verificado durante um período de tempo especificado. São apresentadas as estatísticas para os 100 URLs superiores para estes tipos de elementos diretamente abaixo este gráfico.
Detalhes CONFIG_NOCACHE | Contém um gráfico que apresenta os URLs de 10 principais para elementos que não foram colocadas em cache devido a configuração de CDN do cliente. Estes tipos de activos foram servidos diretamente a partir do servidor de origem. São apresentadas as estatísticas para os 100 URLs superiores para estes tipos de elementos diretamente abaixo este gráfico.
Detalhes UNCACHEABLE | Contém um gráfico que apresenta os 10 URLs superiores para recursos que podem não ser colocada em cache devido a dados do cabeçalho pedido. São apresentadas as estatísticas para os 100 URLs superiores para estes tipos de elementos diretamente abaixo este gráfico.
Detalhes TCP_HIT | Contém um gráfico que apresenta os 10 URLs superiores para recursos são servidos imediatamente da cache. São apresentadas as estatísticas para os 100 URLs superiores para estes tipos de elementos diretamente abaixo este gráfico.
Detalhes TCP_MISS | Contém um gráfico que apresenta os 10 URLs superiores para recursos que têm um Estado da cache de TCP_MISS. São apresentadas as estatísticas para os 100 URLs superiores para estes tipos de elementos diretamente abaixo este gráfico.
Detalhes TCP_EXPIRED_HIT | Contém um gráfico que apresenta os 10 URLs principais de activos obsoletos que foram servidos diretamente a partir do POP. São apresentadas as estatísticas para os 100 URLs superiores para estes tipos de elementos diretamente abaixo este gráfico.
Detalhes TCP_EXPIRED_MISS | Contém um gráfico que apresenta os 10 URLs superiores para elementos obsoletos para o qual uma nova versão tinha obtidos a partir do servidor de origem. São apresentadas as estatísticas para os 100 URLs superiores para estes tipos de elementos diretamente abaixo este gráfico.
Detalhes TCP_CLIENT_REFRESH_MISS | Contém um gráfico de barras que apresenta os URLs de 10 principais para elementos foram obtidos a partir de um servidor de origem devido a um pedido de cache não a partir do cliente. São apresentadas as estatísticas para os URLs dos 100 valores superiores para estes tipos de pedidos de diretamente abaixo este gráfico.
Tipos de pedido de cliente | Indica o tipo de pedidos de feitas pelos clientes de HTTP (por exemplo, os browsers). Este relatório inclui um gráfico de anel que fornece uma ideia de como para pedidos de que forma são a ser processados. Informações de largura de banda e o tráfego para cada tipo de pedido são apresentadas abaixo do gráfico.
Agente do utilizador | Contém um gráfico de barras apresentar os principais agentes de 10 utilizador para pedir o seu conteúdo através do nosso CDN. Normalmente, um agente do utilizador é um browser, media player ou num browser do telemóvel. São apresentadas as estatísticas para os principais agentes do 100 utilizador diretamente abaixo este gráfico.
Referências | Contém um gráfico de barras apresentar as 10 principais referências para conteúdo acedido através do nosso CDN. Normalmente, uma referência é o URL da página web ou recurso que liga ao seu conteúdo. Obter informações detalhadas são fornecidas abaixo do gráfico para as referências de início 100.
Tipos de compressão | Contém um gráfico de anel que divide activos pedidos por se foram comprimidos ao nossos servidores edge. A percentagem de activos comprimidos é dividida por tipo de compressão utilizado. Obter informações detalhadas são fornecidas abaixo do gráfico para cada tipo de compressão e o estado.
Tipos de ficheiro | Contém um gráfico de barras que apresenta os tipos de 10 ficheiro principais que foram pedidos através da nossa CDN para a sua conta. Para efeitos deste relatório, um tipo de ficheiro definido pelo extensão de nome de ficheiro do activo e tipo de multimédia de Internet (por exemplo,. HTML \[texto/html\],. htm \[texto/html\],. aspx \[texto/html\], etc.). Obter informações detalhadas são fornecidas abaixo do gráfico para os tipos de ficheiro dos 100 valores superiores.
Ficheiros exclusivos | Contém um gráfico que representa o número total de activos exclusivos que foram solicitados num dia em particular durante um período de tempo especificado.
Resumo do token Auth | Contém um gráfico circular que fornece uma descrição geral sobre se activos pedidos foram protegidos por uma autenticação baseada em Token. Elementos protegidos são apresentados no gráfico de acordo com os resultados da sua tentada autenticação.
Token Auth negar detalhes | Contém um gráfico de barras permite-lhe ver os pedidos de 10 principais que foram negados devido a autenticação baseada em Token.
Códigos de resposta HTTP | Fornece uma divisão HTTP códigos de estado (por exemplo, 200 OK, 403 proibido, 404 não encontrado, etc.) que foram entregues os seus clientes HTTP ao nossos servidores edge. Um gráfico circular permite-lhe rapidamente avaliar como os seus ativos foram servidos. Dados estatísticos detalhados são fornecidos para cada resposta de código abaixo do gráfico.
404 erros | Contém um gráfico de barras permite-lhe ver os pedidos de 10 principais que resultou num 404 não encontrado resposta código.
403 erros | Contém um gráfico de barras permite-lhe ver os pedidos de 10 principais que resultou num código de resposta proibido 403. Um código de resposta proibido 403 ocorre quando um pedido é negado por um servidor de origem de cliente ou um servidor edge no nosso POP.
4xx erros | Contém um gráfico de barras permite-lhe ver os pedidos de 10 principais que resultou num código de resposta no intervalo 400. Excluídos deste relatório são 403 não encontrado e códigos de resposta proibido 404. Normalmente, um código de resposta 4xx ocorre quando um pedido é negado como resultado de um erro de cliente.
504 erros | Contém um gráfico de barras permite-lhe ver os pedidos de 10 principais que resultou num código de resposta 504 tempo limite do Gateway. Um código de resposta de tempo limite do Gateway 504 ocorre quando um limite de tempo quando estiver a tentar um servidor proxy HTTP comunicar com outro servidor. No caso dos nossos CDN, um código de resposta de tempo limite do Gateway 504 normalmente ocorre quando um servidor edge é não é possível estabelecer uma comunicação com um servidor de origem do cliente.
502 erros | Contém um gráfico de barras permite-lhe ver os pedidos de 10 principais que resultou num código de resposta 502 Gateway inválido. Um código de resposta 502 Gateway inválido ocorre quando uma falha do protocolo HTTP entre um servidor e um servidor proxy HTTP. No caso dos nossos CDN, um código de resposta 502 Gateway inválido normalmente ocorre quando um servidor de origem do cliente devolve uma resposta inválida para um servidor edge. Uma resposta é inválida se-não é possível que possam ser analisada ou se está incompleto.
5XX erros | Contém um gráfico de barras permite-lhe ver os pedidos de 10 principais que resultou num código de resposta no intervalo 500.  Excluídos deste relatório são 502 Gateway inválido e 504 códigos de resposta de tempo limite do Gateway.

## <a name="see-also"></a>Consulte também
* [Descrição geral Azure CDN](cdn-overview.md)
* [Estatística em tempo real no Microsoft Azure CDN](cdn-real-time-stats.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)
* [Relatórios HTTP avançada](cdn-advanced-http-reports.md)
