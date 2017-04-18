<properties
    pageTitle="As funções aninhadas perfis do Gestor de tráfego | Microsoft Azure"
    description="Este artigo explica a funcionalidade de 'Perfis aninhada' do Gestor de tráfego do Azure"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="nested-traffic-manager-profiles"></a>Perfis do Gestor de tráfego aninhados

Gestor de tráfego inclui um intervalo de métodos de encaminhamento de tráfego que lhe permitem controlar como o Gestor de tráfego escolhe o ponto final deve receber o tráfego de cada utilizador final. Para mais informações, consulte o artigo [Gestor de tráfego de encaminhamento de tráfego métodos](traffic-manager-routing-methods.md).

Cada perfil do Gestor de tráfego Especifica um método de encaminhamento de tráfego único. No entanto, existem cenários que requerem que o encaminhamento fornecida por um perfil do Gestor de tráfego única a encaminhamento de tráfego mais sofisticadas. Pode aninhar perfis do Gestor de tráfego para combinar as vantagens de mais do que um método de encaminhamento de tráfego. Perfis aninhados permitem-lhe substituir o comportamento de tráfego gestor predefinido para suporte maior e mais complexas implementações da aplicação.

Os exemplos seguintes mostram como utilizar perfis do Gestor de tráfego aninhadas em diversos cenários.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemplo 1: Combinar encaminhamento de tráfego de 'Desempenho' e 'Ponderado'

Imaginemos que implementada uma aplicação nas seguintes regiões Azure: Ocidental EUA, Europa Oeste e Ásia. Utilize o método de encaminhamento de tráfego de 'Desempenho' em do Gestor de tráfego para distribuir o tráfego para a região mais próxima ao utilizador.

![Perfil do Gestor de tráfego único][1]

Agora, suponha que pretende testar uma atualização para o seu serviço antes de gradual muito mais. Que pretende utilizar o método de encaminhamento de tráfego 'ponderado' para direcionar uma pequena percentagem de tráfego de para a sua implementação de teste. Configurar a implementação de teste juntamente com a implementação de produção existente na Europa Ocidental.

Não é possível combinar ambos os 'ponderado' e ' desempenho-encaminhamento de tráfego num único perfil. Para suportar neste cenário, crie um perfil do Gestor de tráfego utilizar os dois pontos finais da Europa Oeste e o método de encaminhamento de tráfego de 'Ponderado'. Em seguida, adicione este perfil 'criança' como um ponto final para o perfil 'principal'. O perfil principal ainda utiliza o método de encaminhamento de tráfego de desempenho e contém outras implementações globais como pontos finais.

O diagrama seguinte ilustra neste exemplo:

![Perfis do Gestor de tráfego aninhados][2]

Nesta configuração, o tráfego dirigido através do perfil de elemento principal distribui tráfego através de regiões normalmente. Na Europa Ocidental, o perfil aninhado distribui o tráfego para os pontos finais de produção e teste de acordo com as espessuras atribuídos.

Quando o perfil principal utiliza o método de encaminhamento de tráfego de 'Desempenho', cada ponto final tem de ser atribuída uma localização. A localização é atribuída ao configurar o ponto final. Selecione a região Azure mais próxima da sua implementação. As regiões Azure são os valores de localização suportados pela tabela de latência da Internet. Para obter mais informações, consulte o artigo [Gestor de tráfego 'Desempenho' Encaminhamento de tráfego método](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemplo 2: Monitorização de ponto final nos perfis aninhado

Gestor de tráfego ativamente monitoriza o funcionamento de cada ponto final de serviço. Se um ponto final está danificado, Gestor de tráfego encaminha os utilizadores para os pontos finais alternativos preservar a disponibilidade do seu serviço. Este comportamento de monitorização e activação pós-falha de ponto final aplica-se a todos os métodos de encaminhamento de tráfego. Para obter mais informações, consulte [Gestor de tráfego de ponto final de monitorização](traffic-manager-monitoring.md). Monitorização de ponto final funciona de forma diferente para perfis aninhados. Com os perfis de aninhadas, o perfil principal não executar verificações de integridade na criança diretamente. Em vez disso, o estado de funcionamento de pontos finais do perfil subordinado é utilizado para calcular o estado de funcionamento geral do perfil subordinado. Estas informações de estado de funcionamento são propagadas para cima na hierarquia de perfil aninhadas. O perfil principal este agregado do Estado de funcionamento para determinar se para o tráfego direto para o perfil de subordinados. Consulte a secção de [Perguntas mais frequentes](#faq) deste artigo para obter detalhes completos sobre como monitorizar o estado de funcionamento dos perfis aninhados.

Voltar no exemplo anterior, imaginemos que a implementação de produção na Europa Ocidental falha. Por predefinição, o perfil 'criança' encaminha todo o tráfego para a implementação de teste. Se a implementação de teste também falhar, o perfil principal determina se o perfil de subordinados não deve receber tráfego de uma vez que todos os pontos finais de subordinados são danificados. Em seguida, o perfil principal distribui o tráfego para as outras regiões.

![Aninhadas perfil activação pós-falha (comportamento predefinido)][3]

Poderá ser satisfeito com este disposição. Ou pode estar interessado que todo o tráfego para Europa Ocidental agora vai de implementação de teste em vez de tráfego de subconjunto limitado. Independentemente do Estado de funcionamento da implementação teste, que pretende sobre a falhar outras regiões quando a implementação de produção na Europa Ocidental falha. Para permitir que este activação pós-falha, pode especificar o parâmetro 'MinChildEndpoints' ao configurar o perfil subordinado como um ponto final no perfil de elemento principal. O parâmetro determina o número mínimo de pontos finais disponíveis no perfil de subordinados. O valor predefinido é '1'. Para este cenário, defina o valor de MinChildEndpoints para 2. Abaixo deste limiar, o perfil principal leva em consideração o perfil de subordinados toda a não estar disponível e encaminha o tráfego para os pontos finais.

A figura seguinte ilustra esta configuração:

![As funções aninhadas perfil failover com 'MinChildEndpoints' = 2][4]

>[AZURE.NOTE]
>O método de encaminhamento de tráfego de 'Priority' distribui todo o tráfego para um ponto final de único. Assim, há pouco finalidade numa definição de MinChildEndpoints diferente de '1' de um perfil de subordinados.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemplo 3: Prioridades activação pós-falha regiões encaminhamento de tráfego de 'Desempenho'

O comportamento predefinido para o método de encaminhamento de tráfego de 'Desempenho' foi concebido para evitar indevidamente carregamento da próxima mais próximo de ponto final e a causar uma série de falhas em cascata. Quando um ponto final falha, todo o tráfego que iria direcionado para esse ponto final é distribuído uniformemente para os pontos finais ao longo de todas as regiões.

![Encaminhamento com predefinido activação pós-falha de tráfego de 'Desempenho'][5]

No entanto, imaginemos que prefere o tráfego de Europa Ocidental activação pós-falha EUA Ocidental e apenas direcionar tráfego para outras regiões quando ambos os pontos finais não estão disponíveis. Pode criar esta solução utilizando um perfil de subordinado com o método de encaminhamento de tráfego de 'Priority'.

![Encaminhamento com preferencial activação pós-falha de tráfego de 'Desempenho'][6]

Uma vez que o ponto final da Europa Oeste tem prioridade mais alta que o ponto final oeste EUA, todo o tráfego é enviado para o ponto final da Europa Oeste quando ambos os pontos finais estão online. Se Europa Ocidental falhar, o tráfego é direcionado para EUA Ocidental. Com o perfil aninhado, o tráfego é direcionado para Ásia apenas quando Europa Oeste e EUA Ocidental falhar.

Pode repetir este padrão para todas as regiões. Substitua todos os três pontos finais no perfil de elemento principal três perfis subordinado, cada fornecer numa sequência com prioridade alta activação pós-falha.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemplo 4: Controlar o encaminhamento de tráfego 'Desempenho' entre vários pontos finais na mesma região

Suponha que o desempenho' ' Encaminhamento de tráfego método é utilizado no perfil que tenha mais do que um ponto final numa região específico. Por predefinição, o tráfego dirigido a região é distribuído uniformemente em todos os pontos finais disponíveis na região.

![Tráfego 'Desempenho' Encaminhamento de distribuição de tráfego na região (comportamento predefinido)][7]

Em vez de adicionar vários pontos finais da Europa Ocidental, esses pontos finais estão entre um perfil de subordinados em separado. O perfil subordinado é adicionado ao principal, como o ponto final apenas na Europa Ocidental. As definições de perfil de filho podem controlar a distribuição de tráfego com Europa Oeste ao ativar o encaminhamento de tráfego baseado na prioridade ou ponderado nessa região.

![Encaminhamento com distribuição personalizado na região tráfego de tráfego de 'Desempenho'][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemplo 5: Definições de monitorização por ponto final

Suponha que estiver a utilizar o Gestor de tráfego de migrar sem problemas tráfego a partir de um legado no local web site para uma nova versão baseada na nuvem alojada no Azure. Para o site legado, que pretende utilizar na home page URI para monitorizar o estado de funcionamento do site. Mas para a nova versão baseada na nuvem, estão a implementar uma página de monitorização personalizada (caminho ' / monitor.aspx') que inclua verificações adicionais.

![Ponto final do Gestor de tráfego monitorização (comportamento predefinido)][9]

As definições de monitorização no perfil do Gestor de tráfego de aplicam a todos os pontos finais dentro de um único perfil. Com os perfis de aninhadas, utilize um perfil diferente subordinado por site para definir diferentes definições de monitorização.

![Ponto final do Gestor de tráfego de monitorização com definições de ponto-final][10]

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES

### <a name="how-do-i-configure-nested-profiles"></a>Como configurar o perfis aninhados?

Perfis do Gestor de tráfego aninhadas podem ser configurados utilizando o Gestor de recursos do Azure e clássica Azure REST API do, os cmdlets do PowerShell do Azure e comandos do Azure clip em diferentes plataformas. Também são suportados através do portal do Azure novo. Não são suportadas no portal do clássico.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Quantos camadas de aninhamento faz Gestor de tráfego suportam?

Pode aninhar perfis até 10 níveis de profundidade. 'Ciclos' não são permitidos.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Pode misturar outros tipos de ponto final com os perfis de subordinado aninhado, no perfil do Gestor de tráfego mesmo?

Sim. Não existem restrições sobre como o utilizador combina os pontos finais de diferentes tipos dentro de um perfil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Como é que o modelo de faturação aplicam-se aninhadas perfis de?

Não existe não negativo preços impacto da utilização de perfis de aninhadas.

Gestor de tráfego faturação têm dois componentes: verificações de integridade de ponto final e milhões de consultas de DNS

- Verificações de integridade de ponto final: não existe nenhuma encargo de um perfil de subordinados quando configurado como um ponto final de um perfil de elemento principal. Monitorizar os pontos finais no perfil subordinado são faturada em como habitualmente.
- Consultas de DNS: cada consulta só é contada uma vez. Uma consulta de um perfil de elemento principal que devolve um ponto final de um perfil subordinado é contabilizada contra apenas o perfil de elemento principal.

Para detalhes completos, consulte a [página de preços do Gestor de tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Existe um impacto no desempenho aninhados perfis de?

Não. Não existe nenhuma impacto no desempenho suportado quando utilizar perfis aninhados.

Os servidores de nomes do Gestor de tráfego percorrer a hierarquia de perfil internamente quando está a processar cada consulta DNS. Uma consulta DNS para um perfil de elemento principal pode receber uma resposta DNS com um ponto final de um perfil de subordinados. Um único registo CNAME é utilizado se estiver a utilizar um único perfil ou perfis aninhados. Não é necessário para criar um registo CNAME para cada perfil na hierarquia.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Como é que o Gestor de tráfego calcular o estado de funcionamento de um ponto final de aninhadas num perfil principal?

O perfil principal não executa verificações de integridade na criança diretamente. Em vez disso, o estado de funcionamento de pontos finais do perfil subordinado são utilizadas para calcular o estado de funcionamento geral do perfil subordinado. Esta informação é propagada para cima na hierarquia de perfil aninhadas para determinar o estado de funcionamento do ponto final aninhado. O perfil principal utiliza este estado de funcionamento do agregado para determinar se o tráfego pode ser direcionado para a criança.

A tabela seguinte descreve o comportamento do Gestor de tráfego de estado de funcionamento do verifica se ocorre um ponto final de aninhadas.

|Estado de Monitor de perfil de subordinados|Estado de Monitor de ponto final principal|Notas|
|---|---|---|
|Desativado. O perfil subordinado foi desativado.|Parado|O estado de ponto final principal está parado não desativado. O estado desactivado está reservado para indicar que tenha desativado o ponto final no perfil de elemento principal.|
|Degradado. Ponto final de perfil, pelo menos, um subordinado está num Estado degradado.| Online: o número de pontos finais Online no perfil de subordinados, pelo menos, é o valor de MinChildEndpoints.<BR>CheckingEndpoint: o número de pontos finais Online plus CheckingEndpoint no perfil de subordinados, pelo menos, é o valor de MinChildEndpoints.<BR>Degradado: caso contrário.|Tráfego é encaminhado para um ponto final de estado CheckingEndpoint. Se MinChildEndpoints estiver definido demasiado alto, o ponto final é sempre degradado.|
|Online. Ponto final de perfil, pelo menos, um subordinado é um Estado Online. Nenhum ponto final é no estado de degradado.|Consulte o artigo acima.||
|CheckingEndpoints. Ponto final de perfil de pelo menos um filho é 'CheckingEndpoint'. Sem pontos finais são 'Online' ou 'Degradado'|Mesmo que acima.||
|Inativo. Todos os pontos finais perfil de subordinado estão desativada ou parado ou este perfil tem sem pontos finais.|Parado||


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [como funciona o Gestor de tráfego](traffic-manager-how-traffic-manager-works.md)

Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png

