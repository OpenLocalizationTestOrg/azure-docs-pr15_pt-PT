<properties
    pageTitle="Analisar os padrões de utilização do Azure CDN | Microsoft Azure"
    description="Pode ver os padrões de utilização para sua CDN utilizando os seguintes relatórios: largura de banda, os dados transferidos, acertos, os Estados de Cache, a taxa de acertos na Cache, IPV4/IPV6 dados transferidos."
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

# <a name="analyze-azure-cdn-usage-patterns"></a>Analisar os padrões de utilização do Azure CDN

[AZURE.INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

É possível visualizar os padrões de utilização para sua CDN utilizando os seguintes relatórios:

- Largura de banda
- Dados transferidos
- Acertos
- Estados de cache
- Taxa de acertos na cache
- Dados de IPv4/IPV6 transferidos

## <a name="accessing-advanced-http-reports"></a>Aceder a relatórios HTTP avançados

1. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-reports/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

2. Paire sobre o separador de **análise** , em seguida, coloque o cursor sobre a lista de opções de **Relatórios principais** .  Clique no relatório pretendido no menu de lista.

    ![Portal de gestão de CDN - menu Core relatórios](./media/cdn-reports/cdn-core-reports.png)


## <a name="bandwidth"></a>Largura de banda

O relatório de largura de banda é composta por uma tabela de dados e de gráfico que indica a utilização da largura de banda para HTTP e HTTPS sobre um determinado período de tempo. Pode ver a utilização da largura de banda ao longo do todos os CDN POP ou um nomeadamente POP. Permite-lhe ver o tráfego picos e distribuição através de CDN POP no Mbps.

- Selecione todos os nós de limite para ver o tráfego de todos os nós ou selecione um nó/região específico na lista pendente.
- Selecione o intervalo de datas para ver dados para hoje esta semana/este mês, etc., ou introduzir datas personalizadas, em seguida, clique em "Ir" para se certificar de que a sua seleção é atualizada.
- Pode exportar e transferir os dados ao clicar no ícone de folha de excel localizado junto de "Ir".

O relatório for atualizado em 5 minutos.

![Relatório de largura de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Dados transferidos

Este relatório é composta por uma tabela de dados e de gráfico que indica a utilização de tráfego para HTTP e HTTPS sobre um determinado período de tempo. Pode ver a utilização de tráfego através de todos os CDN POP ou um nomeadamente POP. Permite-lhe ver o tráfego picos e distribuição através de CDN POP no GB.

- Selecione todos os nós de limite para ver o tráfego de todas as notas ou selecione um nó/região específico na lista pendente.
- Selecione o intervalo de datas para ver dados para hoje esta semana/este mês, etc., ou introduzir datas personalizadas, em seguida, clique em "Ir" para se certificar de que a sua seleção é atualizada.
- Pode exportar e transferir os dados ao clicar no ícone de folha de excel localizado junto de "Ir".

O relatório for atualizado em 5 minutos.

![Dados transferidos relatório](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Acertos (códigos de estado)

Este relatório descreve a distribuição dos códigos de estado do pedido para o seu conteúdo. Todos os pedidos de conteúdo irão gerar um código de estado HTTP. O código de estado descreve processados limite POP o pedido. Por exemplo, códigos de estado 2xx indicam que o pedido foi servido com êxito para um cliente, enquanto um código de estado 4xx indica que ocorreu um erro. Para obter mais detalhes sobre o código de estado HTTP, consulte o artigo [códigos de estado](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

- Selecione o intervalo de datas para ver dados para hoje esta semana/este mês, etc., ou introduzir datas personalizadas, em seguida, clique em "Ir" para se certificar de que a sua seleção é atualizada.
- Pode exportar e transferir os dados ao clicar na folha de excel localizada junto de "Ir".

![Relatório de acertos](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Estados de cache

Este relatório descreve a distribuição dos acertos e falhas na cache para pedido de cliente. Uma vez que o desempenho mais rápido provêm de acertos na cache, pode otimizar velocidades de entrega de dados ao redução falhas na cache e acertos expirados. Podem ser reduzidas falhas na cache ao configurar o seu servidor de origem para evitar a atribuição de cabeçalhos de resposta "no-cache", por evitar a cadeia de consulta colocação em cache, exceto quando for necessário estritamente e evitando códigos de resposta não cacheable. Cache expirado acertos podem ser evitados ao efetuar um ativo estiverem máximo idade longas quanto possível para minimizar o número de pedidos para o servidor de origem.

![Relatório de Estados de cache](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Estados de cache principal incluem:

- TCP_HIT: Servida a partir do limite. O objeto foi na cache e não tivesse excedido o respetiva idade máximo.
- TCP_MISS: Servida de origem. O objeto não foi na cache e a resposta foi voltar a origem.
- TCP_EXPIRED _MISS: servida de origem depois revalidação com origem. O objecto foi na cache mas tinha excedido o respetiva idade máximo. Uma revalidação com origem resultou o objeto de cache ser substituído por uma nova resposta de origem.
- TCP_EXPIRED _HIT: servida a partir do limite após revalidação com origem. O objecto foi na cache mas tinha excedido o respetiva idade máximo. O objeto de cache a ser inalterado resultou uma revalidação com o servidor de origem.

- Selecione o intervalo de datas para ver dados para hoje esta semana/este mês, etc., ou introduzir datas personalizadas, em seguida, clique em "Ir" para se certificar de que a sua seleção é atualizada.
- Pode exportar e transferir os dados ao clicar no ícone de folha de excel localizado junto de "Ir".

### <a name="full-list-of-cache-statuses"></a>Lista completa dos Estados de cache

- TCP_HIT - este estado é comunicado quando um pedido de é servido diretamente a partir do POP para o cliente. Imediatamente é fornecido um ativo a partir de um POP quando é colocada em cache no POP mais próximo do cliente e tem um válido time to live ou TTL. TTL é determinada pelos cabeçalhos de resposta seguintes:

    - Controlo de cache: s-maxage
    - Controlo de cache: max-idade
    - Expirar

- TCP_MISS - este estado indica que não foi encontrada uma versão do elemento pedida em cache no POP mais próximo do cliente. Elemento será solicitado a partir de um servidor de origem ou um servidor de protecção de origem. Se o servidor de origem ou o servidor de protecção origem devolve um ativo, serão servida para o cliente e em cache no cliente e servidor edge. Caso contrário, um código de estado que não sejam 200 (por exemplo, 403 proibido, 404 não encontrado, etc.) será devolvido.

- TCP_EXPIRED _HIT - este estado é comunicado quando um pedido de atribuído um ativo com um TTL expirado, tal como quando max-idade o activo expirou, foi servido diretamente a partir do POP para o cliente.

    Um pedido de expirados normalmente resulta num pedido de revalidação ao servidor de origem. Ordem de um _HIT TCP_EXPIRED a ocorrer, o servidor de origem tem de indicar que não existe uma versão mais recente do elemento. Este tipo de situação normalmente será atualizada desse activo Cache-Control e cabeçalhos expira.

- TCP_EXPIRED _MISS - este estado é comunicado quando uma versão mais recente de um ativo em cache expirado é servida a partir do POP para o cliente. Isto ocorre quando o TTL para um recurso em cache expirou (por exemplo, expirada máximo idade) e o servidor de origem devolve uma versão mais recente desse activo. Esta nova versão do elemento será servida para o cliente em vez da versão em cache. Para além disso, irá cache no servidor edge e no cliente.

- CONFIG_NOCACHE - este estado indica que uma configuração específicas do cliente no nosso limite POP impedido o activo a ser em cache.

- NENHUM – este estado indica que não foi executada uma verificação de actualização de conteúdos de cache.

- TCP_ CLIENT_REFRESH _MISS - este estado é comunicado quando um cliente HTTP (por exemplo, browser) força um contorno POP para obter uma nova versão de um ativo obsoleto a partir do servidor de origem.

    Por predefinição, os nossos servidores impedem que um cliente HTTP forçar o nossos servidores edge para obter uma nova versão do elemento do servidor de origem.

- TCP_ PARTIAL_HIT - este estado é comunicado quando um pedido de intervalo byte resulta num sucesso por um activo parcialmente em cache. O intervalo de byte pedido é servido imediatamente a partir do POP para o cliente.

- UNCACHEABLE - este estado é comunicado quando os cabeçalhos de Cache-Control e expira de um ativo indicam que-deve não ser colocadas em cache no POP ou pelo cliente HTTP. Estes tipos de pedidos de são servidos a partir do servidor de origem

## <a name="cache-hit-ratio"></a>Taxa de acertos na cache

Este relatório indica a percentagem de pedidos em cache que foram servida diretamente da cache.

O relatório fornece os seguintes detalhes:

- O conteúdo solicitado foi colocada em cache no POP o autor da requisição mais próximo.
- O pedido foi servido diretamente a partir da extremidade da nossa rede.
- O pedido não foi necessário revalidação com o servidor de origem.

O relatório não inclui:

- Solicita que é negada devido a país/região opções de filtragem.
- Pedidos de activos cujos cabeçalhos indicam não devem ser colocados. Por exemplo, controlo de Cache: privado, Cache controlo: não cache ou Pragma: cabeçalhos de cache não irão impedir que um ativo colocada em cache.
- Pedidos de intervalo byte para conteúdo parcialmente em cache.

A fórmula é: (TCP_ ACERTOS / (TCP_ ACERTOS + TCP_MISS)) * 100

- Selecione o intervalo de datas para ver dados para hoje esta semana/este mês, etc., ou introduzir datas personalizadas, em seguida, clique em "Ir" para se certificar de que a sua seleção é atualizada.
- Pode exportar e transferir os dados ao clicar no ícone de folha de excel localizado junto de "Ir".


![Taxa de acertos relatório em cache](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Dados de IPv4/IPV6 transferidos

Este relatório mostra a distribuição de utilização de tráfego no IPV4 vs IPV6.

![Dados de IPv4/IPV6 transferidos](./media/cdn-reports/cdn-ipv4-ipv6.png)

- Selecione o intervalo de datas para ver dados para hoje esta semana/este mês, etc., ou introduza datas personalizadas.
- Em seguida, clique em "aceda" para se certificar de que a sua seleção é atualizada.


## <a name="considerations"></a>Considerações sobre

Só podem ser gerados relatórios nos últimos 18 meses.
