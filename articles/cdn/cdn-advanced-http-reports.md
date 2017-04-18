<properties
    pageTitle="Azure CDN avançadas HTTP relatórios | Microsoft Azure"
    description="Relatórios HTTP avançados no Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas na atividade CDN."
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

# <a name="advanced-http-reports-in-microsoft-azure-cdn"></a>Relatórios HTTP avançados no Microsoft Azure CDN

## <a name="overview"></a>Descrição geral

Este documento explica avançadas HTTP elaboração de relatórios no Microsoft Azure CDN. Estes relatórios fornecem informações detalhadas na atividade CDN.

[AZURE.INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="accessing-advanced-http-reports"></a>Aceder a relatórios HTTP avançados

1. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-advanced-http-reports/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

2. Paire sobre o separador de **análise** , em seguida, coloque o cursor sobre a lista de opções de **Relatórios de HTTP avançadas** .  Clique em **HTTP grandes**plataforma.

    ![Portal de gestão de CDN - menu Relatórios avançadas](./media/cdn-advanced-http-reports/cdn-advanced-reports.png)

    São apresentadas as opções de relatório.

## <a name="geography-reports-map-based"></a>Relatórios de geografia (baseados em mapas)

Existem cinco relatórios tirar partido de um mapa para indicar as regiões a partir do qual o seu conteúdo está a ser pedido. Estes relatórios são mapas do mundo, mapa dos Estados Unidos, Canadá mapa, Europa mapa e países da Ásia do Pacífico mapa.

Cada relatório baseados em mapas ordena entidades geográficas (por exemplo, países/regiões, Estados e províncias) de acordo com a percentagem de acertos teve origem a partir dessa região. Para além disso, um mapa é fornecido para ajudá-lo a visualizar as localizações a partir do qual o seu conteúdo está a ser pedido. É possível fazê-lo por cada região de acordo com a quantidade de pedido experiente da região de codificação de cores. Mais clara sombreados regiões indicam pedido inferior para o seu conteúdo, enquanto regiões mais escuras indicam níveis mais altos da procura para o seu conteúdo.

As informações detalhadas sobre o tráfego e largura de banda para cada região são fornecidas diretamente abaixo o mapa. Esta opção permite-lhe ver o número total de acertos, a percentagem de acertos, a quantidade total de dados transferidas (em gigabytes) e, a percentagem de dados transferidos para cada região. Ver uma descrição para cada um dos seguintes métricas. Por fim, quando passar o rato sobre uma região (por exemplo, país/região, distrito ou concelho), o nome e a percentagem de acertos que ocorreram na região serão apresentados como uma descrição.

Uma breve descrição é fornecida abaixo para cada tipo de relatório de geografia baseados em mapas.

Nome do relatório | Descrição
------------|------------
Mapa Mundial | Este relatório permite-lhe ver a nível mundial procura para o seu conteúdo CDN. Cada país tem códigos de cores no mapa mundial para indicar a percentagem de acertos originária de região.
Mapa de Estados Unidos | Este relatório permite-lhe ver a procura para o seu conteúdo CDN nos Estados Unidos. Cada Estado é com códigos de cores neste mapa para indicar a percentagem de acertos originária de região.
Mapa de Canadá | Este relatório permite-lhe ver a procura para o seu conteúdo CDN no Canadá. Cada distrito é com códigos de cores neste mapa para indicar a percentagem de acertos originária de região.
Mapa da Europa | Este relatório permite-lhe ver a procura para o seu conteúdo CDN na Europa. Cada país são com códigos de cores neste mapa para indicar a percentagem de acertos originária de região.
Mapa do Pacífico países da Ásia | Este relatório permite-lhe ver a procura para o seu conteúdo CDN na Ásia. Cada país são com códigos de cores neste mapa para indicar a percentagem de acertos originária de região.

## <a name="geography-reports-bar-charts"></a>Relatórios de geografia (gráficos de barras)

Existem dois relatórios adicionais que fornecem informações estatísticas de acordo com a geografia, que são cidades de início e dos países da parte superior. Estes relatórios classificar cidades e países/regiões, respetivamente, de acordo com o número de acertos originária dessas regiões. Relativamente a gerar neste tipo de relatório, um gráfico de barras irá indicar a 10 cidades ou países/regiões que pediram conteúdo através de uma plataforma específica. Este gráfico de barras permite-lhe avaliar rapidamente as regiões que geram o número mais alto de pedidos para o seu conteúdo.

O lado esquerdo do gráfico (eixo dos YY) indica acertos quantos ocorreram na região especificado. Diretamente abaixo do gráfico de eixo (x), encontrará uma etiqueta para cada uma das 10 regiões superiores.

### <a name="using-the-bar-charts"></a>Utilizar gráficos de barras

* Se passar o rato sobre uma barra, o nome e o número total de acertos que ocorreram na região serão apresentadas como uma descrição.
* Uma descrição para o relatório de cidades início identifica uma cidade ao seu nome, estado/província e abreviatura do país.
* Se não foi possível determinar a cidade ou região (por exemplo, estado/província) a partir da qual um pedido de teve origem, irá indicar que estes provêm desconhecidas. Se o país/região for desconhecido, em seguida, dois pontos de interrogação (isto é,??), será apresentada.
* Um relatório pode incluir métricas para "Europa" ou "Região Ásia-Pacífico". Esses itens não se destinam a fornecer informações estatísticas sobre todos os endereços IP nessas regiões. Em vez disso, eles apenas serem aplicadas a pedidos oriundos endereços IP expandir sobre Europa ou Ásia-Pacífico/em vez da uma cidade específica ou a país.

Os dados que foram utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existem irá encontrar o número total de acertos, a percentagem de acertos, a quantidade de dados transferidas (em gigabytes) e a percentagem de dados transferidos para as regiões de início 250. Ver uma descrição para cada um dos seguintes métricas.

Uma breve descrição é fornecida para ambos os tipos de relatórios abaixo.

Nome do relatório | Descrição
------------|------------
Início cidades | Este relatório ordena cidades de acordo com o número de acertos teve origem a partir dessa região.
Países/regiões superiores | Este relatório ordena países/regiões de acordo com o número de acertos teve origem a partir dessa região.

## <a name="daily-summary"></a>Resumo diário

O relatório de resumo diária permite-lhe ver o número total de acertos e dados transferidos através de uma determinada plataforma diariamente. Estas informações podem ser utilizadas para rapidamente identificação padrões de atividade CDN. Por exemplo, este relatório pode ajudá-lo detetar os dias que experientes superior ou inferiores tráfego esperado.

Relativamente a gerar neste tipo de relatório, um gráfico de barras irá fornecer uma indicação visual Control a quantidade de pedido de específicos da plataforma experiente diariamente durante o período de tempo abrangido pelo relatório. Irá fazê-lo ao apresentar uma barra para cada dia no relatório. Por exemplo, selecionar o período de tempo denominado "Semana passada" irá gerar um gráfico de barras com barras de sete. Cada barra irá indicar o número total de acertos experientes desse dia.

O lado esquerdo do gráfico (eixo dos YY) indica acertos quantos ocorreram na data especificada. Diretamente abaixo do gráfico de eixo (x), encontrará uma etiqueta que indica a data (formato: aaaa-MM-DD) para cada dia incluído no relatório.

> [AZURE.TIP] Se passar o rato sobre uma barra, o número total de acertos que ocorreram nessa data vai ser apresentado como uma descrição.

Os dados que foram utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existem irá encontrar o número total de acertos e a quantidade de dados transferidas (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-hour"></a>Por hora

O relatório por hora permite-lhe ver o número total de acertos e dados transferidos através de uma plataforma específica numa base de hora a hora. Estas informações podem ser utilizadas para rapidamente identificação padrões de atividade CDN. Por exemplo, este relatório pode ajudá-lo detetar os períodos de tempo durante o dia que experimentar superiores ou inferiores tráfego esperado.

Relativamente a gerar neste tipo de relatório, um gráfico de barras irá fornecer uma indicação visual Control a quantidade de pedido de específicos da plataforma ocorreu numa base de hora a hora durante o período de tempo abrangido pelo relatório. Irá fazê-lo ao apresentar uma barra para cada hora abrangida pelo relatório. Por exemplo, selecionar uma 24 horas período de tempo irá gerar um gráfico de barras com barras de vinte e quatro. Cada barra irá indicar o número total de acertos experientes durante essa hora.

O lado esquerdo do gráfico (eixo dos YY) indica acertos quantos ocorreram na hora especificada. Diretamente abaixo do gráfico de eixo (x), encontrará uma etiqueta que indica a data/hora (formato: aaaa-MM-DD HH) para cada hora incluída no relatório. Tempo é comunicado utilizando o formato de 24 horas e especificado utilizando o fuso horário UTC/GMT.

> [AZURE.TIP] Se passar o rato sobre uma barra, o número total de acertos que ocorreram durante a essa hora vai ser apresentado como uma descrição.

Os dados que foram utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existem irá encontrar o número total de acertos e a quantidade de dados transferidas (em gigabytes) para cada hora abrangida pelo relatório.

## <a name="by-file"></a>Ao ficheiro

O relatório por ficheiro permite-lhe ver a quantidade de procura e o tráfego suportados com o ao longo de uma plataforma específica para os ativos mais solicitadas. Relativamente a gerar neste tipo de relatório, um gráfico de barras será gerado as 10 principais em elementos mais solicitadas durante o período de tempo especificado.

> [AZURE.NOTE] Para efeitos deste relatório, limite CNAME URLs são convertidas em respectivos equivalente URLs de CDN. Isto permite uma precisa de concordância entre para o número total de acertos associada de um ativo, independentemente da extremidade URL CNAME utilizada para pedi-la ou CDN.

O lado esquerdo do gráfico (eixo dos YY) indica o número de pedidos para cada imobilizado durante o período de tempo especificado. Diretamente abaixo do gráfico de eixo (x), irá encontrar uma etiqueta que indica o nome do ficheiro para cada um dos ativos pedidos 10 superiores.

Os dados que foram utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existem irá encontrar as seguintes informações para cada um dos ativos pedidos 250 superiores: caminho relativo, o número total de acertos, a percentagem de acertos, a quantidade de dados transferidas (em gigabytes) e a percentagem de dados transferidos.

## <a name="by-file-detail"></a>Por ficheiro detalhe

O relatório de detalhes do ficheiro ao permite-lhe ver a quantidade de procura e o tráfego suportados com o ao longo de uma plataforma específica para um item específico. Na parte superior deste relatório é a opção ficheiro detalhes para. Esta opção fornece uma lista dos seus ativos mais solicitadas a plataforma selecionado. Para gerar um relatório de detalhes do ficheiro ao, terá de selecionar o elemento pretendido a partir da opção ficheiro detalhes para. Após o qual, um gráfico de barras irá indicar a quantidade de diária pedido que-gerada durante o período de tempo especificado.

O lado esquerdo do gráfico (eixo dos YY) indica o número total de pedidos de que ocorreu a um ativo num dia em particular. Diretamente abaixo do gráfico de eixo (x), encontrará uma etiqueta que indica a data (formato: aaaa-MM-DD) para o qual CDN pedido para o ativo foi comunicou.

Os dados que foram utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existem irá encontrar o número total de acertos e a quantidade de dados transferidas (em gigabytes) para cada dia abrangido pelo relatório.

## <a name="by-file-type"></a>Por tipo de ficheiro

O relatório por tipo de ficheiro permite-lhe ver a quantidade de procura e o tráfego suportados por tipo de ficheiro. Relativamente a gerar neste tipo de relatório, um gráfico em anel irá indicar a percentagem de acertos gerado pelos tipos de 10 ficheiro superior.

> [AZURE.TIP] Se passar o rato sobre um setor do gráfico em anel, Internet de tipo de multimédia de que tipo de ficheiro será apresentado como uma descrição.

Os dados que foram utilizados para gerar o gráfico em anel podem ser visualizados abaixo do mesmo. Não existem irá encontrar o tipo de multimédia de Internet/extensão de nome de ficheiro, o número total de acertos, a percentagem de acertos, a quantidade de dados transferidas (em gigabytes) e a percentagem de dados transferidos para cada um dos tipos de ficheiro início 250.

## <a name="by-directory"></a>Ao diretório

O relatório ao diretório permite-lhe ver a quantidade de procura e o tráfego suportados com o ao longo de uma plataforma específica para o conteúdo de um diretório específico. Relativamente a gerar neste tipo de relatório, um gráfico de barras irá indicar o número total de acertos gerado pelo conteúdo nos 10 directórios superiores.

### <a name="using-the-bar-chart"></a>Utilizar o gráfico de barras

* Paire sobre uma barra para ver o caminho relativo ao diretório correspondente.
* O conteúdo armazenado numa subpasta de um diretório não contam ao calcular o pedido pelo diretório. Este cálculo baseia-se apenas no número de pedidos de gerados para conteúdo armazenado no diretório real.
* Para efeitos deste relatório, limite CNAME URLs são convertidas em respectivos equivalente URLs de CDN. Isto permite uma precisa de concordância entre para todas as estatísticas associada de um ativo, independentemente da extremidade URL CNAME utilizada para pedi-la ou CDN.

O lado esquerdo do gráfico (eixo dos YY) indica o número total de pedidos dos conteúdos armazenados no seu directórios de 10 principais. Cada barra no gráfico representa um diretório. Utilize o esquema de codificação de cores para corresponder para cima uma barra para um diretório listado na secção directórios completo do início 250.

Os dados que foram utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existem irá encontrar as seguintes informações para cada um dos directórios início 250: caminho relativo, o número total de acertos, a percentagem de acertos, a quantidade de dados transferidas (em gigabytes) e a percentagem de dados transferidos.

## <a name="by-browser"></a>Pelo Browser

O relatório pelo Browser permite-lhe ver que browsers foram utilizadas para pedir a conteúdo. Relativamente a gerar neste tipo de relatório, um gráfico circular irá indicar a percentagem de pedidos de geridas pelos browsers de 10 superiores.

### <a name="using-the-pie-chart"></a>Utilizar o gráfico circular

* Paire o cursor sobre um setor no gráfico circular para ver o nome e a versão de um browser.
* Para efeitos deste relatório, cada combinação exclusivo/versão do browser é considerada um browser diferente.
* No setor denominado "Outros" indica a percentagem de pedidos de efetuadas por todos os outros browsers e versões.

Os dados que foram utilizados para gerar o gráfico circular podem ser visualizados abaixo do mesmo. Não existem irá encontrar o número de tipo/versão do browser, o número total de acertos e a percentagem de acertos para cada um dos browsers início 250.

## <a name="by-referrer"></a>Por referência

O relatório por referência permite-lhe ver as principais referências ao conteúdo a plataforma selecionado. Uma referência indica o nome do anfitrião a partir do qual foi criado a um pedido. Relativamente a gerar neste tipo de relatório, um gráfico de barras irá indicar a quantidade de pedido (por exemplo, acertos) gerado pelas 10 principais referências.

O lado esquerdo do gráfico (eixo dos YY) indica o número total de pedidos de que ocorreu a um ativo para cada referência. Cada barra no gráfico representa uma referência. Utilize o esquema de codificação de cores para corresponder para cima uma barra para uma referência listada na secção referência de 250 início.

Os dados que foram utilizados para gerar o gráfico de barras podem ser visualizados abaixo do mesmo. Não existem irá encontrar o URL, o número total de acertos e a percentagem de acertos geradas de cada uma das principais a referências de 250.

## <a name="by-download"></a>Por transferência

O relatório ao transferir permite-lhe analisar os padrões de transferência para o seu conteúdo mais solicitado. Início do relatório contém um gráfico de barras que compara tentou transferências com concluídas transferências para os ativos pedidas 10 superiores. Cada barra tem códigos de cores acordo com se tratar de uma transferência tentada (azul) ou uma transferência concluída (verde).

> [AZURE.NOTE] Para efeitos deste relatório, limite CNAME URLs são convertidas em respectivos equivalente URLs de CDN. Isto permite uma precisa de concordância entre para todas as estatísticas associada de um ativo, independentemente da extremidade URL CNAME utilizada para pedi-la ou CDN.

O lado esquerdo do gráfico (eixo dos YY) indica o nome do ficheiro para cada um dos ativos pedidos 10 superiores. Diretamente abaixo do gráfico de eixo (x), irá encontrar etiquetas que indicam o número total de transferências de tentativa/concluída.

Diretamente abaixo o gráfico de barras, as seguintes informações estará listadas para os ativos pedidos 250 superiores: caminho relativo (incluindo o nome do ficheiro), o número de vezes foi transferida para conclusão, o número de vezes que foi pedido e a percentagem de pedidos de que resultou num concluir a transferência.

> [AZURE.TIP] Não é informado de nossos CDN por um cliente de HTTP (ou seja, browser) quando um ativo foi totalmente transferido. Como resultado, temos que calcular se um recurso foi totalmente transferido acordo com os códigos de estado e intervalo de bytes pedidos. A primeira coisa que olharmos para quando efetuar este cálculo é se o pedido resulta um código de estado OK 200. Se Sim, em seguida, podemos observe pedidos de intervalo de bytes para se certificar de que abrangem elemento inteiro. Por fim, vamos compare a quantidade de dados transferidos para o tamanho do elemento pedido. Se os dados transferidos forem igual ou maior que o tamanho do ficheiro e os pedidos de intervalo de bytes são adequados para esse recurso, o sucesso na vai ser contado como uma transferência concluída.
>
>Devido à natureza interpretive este relatório, deve manter em mente os seguintes pontos que podem alterar a consistência e a precisão deste relatório.
>
>* Não é possível previstos com exatidão padrões de tráfego quando agentes do utilizador se comportam de forma diferente. Isto pode produzir resultados de transferência concluídas que sejam maiores do que 100%.
>* Não podem ser representados com exatidão activos tirar partido das HTTP gradual transferir por este relatório. Este é devido a utilizadores que procuram diferentes posições num vídeo.

## <a name="by-404-errors"></a>Por 404 erros

O relatório de erros em pelo 404 permite-lhe identificar o tipo de conteúdo que gera o maior número de 404 não encontrado os códigos de estado. Início do relatório contém um gráfico de barras para os ativos de 10 principais para a qual foi devolvido um código de estado não encontrado 404. Este gráfico de barras compara o número total de pedidos com pedidos de que resultou num 404 não encontrado código de estado para esses elementos. Cada barra é com códigos de cores. Uma barra amarela é utilizada para indicar que o pedido resultou num 404 não encontrado Estado código. Uma barra vermelha é utilizada para indicar o número total de pedidos de elemento.

> [AZURE.NOTE] Para efeitos deste relatório, tenha em atenção o seguinte procedimento:
>
>* Um sucesso na representa qualquer pedido de um ativo, independentemente do código de estado.
>* Limite CNAME URLs são convertidas em respectivos equivalente URLs de CDN. Isto permite uma precisa de concordância entre para todas as estatísticas associada de um ativo, independentemente da extremidade URL CNAME utilizada para pedi-la ou CDN.

O lado esquerdo do gráfico (eixo dos YY) indica o nome do ficheiro para cada uma das principais 10 activos solicitados que resultou num 404 não encontrado Estado código. Diretamente abaixo do gráfico de eixo (x), irá encontrar etiquetas que indicam o número total de pedidos e o número de pedidos de que resultou num 404 não encontrado Estado código.

Diretamente abaixo o gráfico de barras, as seguintes informações estará listadas para os ativos pedidos 250 superiores: caminho relativo (incluindo o nome do ficheiro), o número de pedidos que resultou num 404 não encontrado Estado código, o número total de vezes que foram pedido elemento e a percentagem de pedidos de que resultou num 404 não encontrado Estado código.

## <a name="see-also"></a>Consulte também
* [Descrição geral Azure CDN](cdn-overview.md)
* [Estatística em tempo real no Microsoft Azure CDN](cdn-real-time-stats.md)
* [Substituir o comportamento HTTP predefinido utilizando o motor de regras](cdn-rules-engine.md)
* [Analisar o desempenho do limite](cdn-edge-performance.md)
