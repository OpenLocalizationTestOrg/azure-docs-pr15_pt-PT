<properties
    pageTitle="Escolher um SKU ou preços camada para a pesquisa do Azure | Microsoft Azure"
    description="Azure pesquisa pode ser aprovisionada nestes SKUs: livre, Basic e padrão, está disponível em vários configurações do recurso e níveis de capacidade onde padrão."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/24/2016"
    ms.author="heidist"/>

# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Escolher um SKU ou preços camada para a pesquisa do Azure

Na pesquisa de Azure, um [serviço está aprovisionado](search-create-service-portal.md) numa camada comparar específica ou SKU. Opções incluem **livre**, **Basic**ou **padrão**, onde **padrão** está disponível em várias configurações e capacidades. 

O objetivo deste artigo é ajudar a escolher uma camada. Se a capacidade de uma camada fica demasiado baixo, terá de aprovisionar um novo serviço na camada superior e, em seguida, voltar a carregar os índices. Não existe nenhuma atualização no local do mesmo serviço a partir de uma SKU para outra. 

> [AZURE.NOTE] Depois de escolher uma camada e [aprovisionar um serviço de pesquisa](search-create-service-portal.md), pode aumentar réplica e partição conta dentro do serviço. Para obter orientações, consulte o artigo [níveis de recursos de escala para consulta e das cargas de trabalho de indexação](search-capacity-planning.md).

## <a name="how-to-approach-a-pricing-tier-decision"></a>Como abordagem uma definição de preços decisão de camadas

Na pesquisa Azure, a camada determina capacidade, não a disponibilidade de funcionalidades. Em geral, funcionalidades estão disponíveis em cada camada, incluindo as funcionalidades de pré-visualização. Uma exceção é não existe suporte para indexadores numa S3 HD.

> [AZURE.TIP] Recomendamos que sempre aprovisionar um serviço **gratuito** (uma por subscrição, com sem data de validade) para que o facilmente disponível para projetos mais simplificada. Utilizar o serviço de **livre** para testar e avaliação; Crie um segundo serviço cobrar na camada **básicos** ou **padrão** de produção ou maiores teste cargas de trabalho.

Capacidade e custos de executar o serviço aceda mão no mão. Informações neste artigo podem ajudar a decidir qual SKU fornece o saldo à direita, mas qualquer uma das-la para ser úteis, terá de estimativas, pelo menos, aproximadas no seguinte:

- Número e o tamanho dos índices que planeia criar
- Número e o tamanho de documentos para carregar
- Algumas ideia de volume de consulta, em termos consultas por segundo (QPS)

Número e o tamanho são importantes porque limites máximos são atingidos através de um limite de disco rígido a contagem de índices ou documentos num serviço ou recursos (armazenamento ou réplicas) utilizados pelo serviço. O limite real do seu serviço de é consoante o que é utilizado o primeiro: recursos ou objetos.

Com estimativas mão, os passos seguintes devem simplificar o processo:

- **Passo 1** Reveja as descrições de SKU abaixo para saber mais sobre as opções disponíveis.
- **Passo 2** Responda às perguntas abaixo para chegar a uma decisão prévia.
- **Passo 3** Finalize a sua decisão ao rever o disco rígidos limites de armazenamento e preços.

## <a name="sku-descriptions"></a>Descrições de SKU

A tabela seguinte fornece descrições de cada camada. 

Camada|Cenários principais
----|-----------------
**Gratuito**|Um serviço partilhado, sem encargos, utilizado para avaliação, inquérito ou pequenas das cargas de trabalho. Uma vez que está a ser partilhado com outras subscritores, débito de consulta e indexação varia com base nos quem está a utilizar o serviço. Capacidade é pequenas (50 MB ou 3 índices com o 10.000 documentos cada o).
**Básicas**|Produção pequenas das cargas de trabalho no hardware dedicado. Altamente disponível. A capacidade é até 3 réplicas e 1 partição (2 GB).
**S1**|1 padrão suporta flexíveis combinações de partições (12) e réplicas (12), utilizadas para das cargas de trabalho de produção média no hardware dedicado. Pode atribuir a partições e réplicas do combinações suportadas por um número máximo de unidades de pesquisa cobrar 36. Neste nível, partições são 25 GB e QPS é de cerca de 15 consultas por segundo.
**S2**|2 padrão executa maiores produção das cargas de trabalho com as mesmas unidades de 36 pesquisa como S1, mas com a partições com dimensões maiores e réplicas. A este nível partições são 100 GB e QPS é de cerca de 60 consultas por segundo.
**S3**|3 padrão é executado proporcionalmente maiores produção das cargas de trabalho nos sistemas de fim mais elevados, em configurações do até a 12 partições ou 12 réplicas unidades de pesquisa em 36. A este nível partições são 200 GB e QPS é mais de 60 consultas por segundo. 
**S3 HD**|Padrão 3 alta densidade foi concebido para um grande número de índices mais pequenos. Pode ter até 3 partições, na 200 GB. QPS é mais de 60 consultas por segundo. 

> [AZURE.NOTE] Larguras réplica e partição são faturadas saída como unidades de pesquisa (36 unidade máximo por serviço), que imponha um limite inferior eficaz que o que o valor máximo implica do valor nominal. Por exemplo, para utilizar o número máximo de 12 réplicas, tem no máximo de 3 partições (12 * 3 = 36 unidades). Da mesma forma, para utilizar a partições máximas, reduza réplicas para 3. Consulte o artigo [níveis de recursos de escala para consulta e indexação das cargas de trabalho na pesquisa Azure](search-capacity-planning.md) para um gráfico no combinações permitidos.

## <a name="review-limits-per-tier"></a>Revisão dos limites de por camadas

O gráfico seguinte é um subconjunto dos limites de [Limites de serviço do Azure pesquisa](search-limits-quotas-capacity.md). Lista os fatores que provavelmente impacto uma decisão SKU. Pode consultar a este gráfico durante a revisão questões que se seguem.

Recurso|Gratuito|Básicas|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrato de nível de serviço (SLA)|Sem <sup>1</sup> |Sim |Sim  |Sim |Sim  |Sim 
Limites de índice remissivo|3|5|50|200|200|1000 <sup>2</sup>
Limites do documento|10.000 total|1 milhão de serviço|15 milhões por partição |milhões de 60 por partição|120 milhões de por partição |1 milhão de índice remissivo
Partições máximas|N/D |1 |12  |12 |12|3 <sup>2</sup>
Tamanho da partição|Total de 50 MB|2 GB por serviço|25 GB por partição |100 GB por partição (até um máximo de 1.2 TB por serviço)|200 GB por partição (até um máximo de TB 2,4 por serviço)|200 GB (até um máximo de 600 GB por serviço)
Réplicas máximas|N/D |3 |12 |12 |12|12
Consultas por segundo|N/D|~ 3 por réplica|~ 15 por réplica|~ 60 por réplica|> 60 por réplica|> 60 por réplica

<sup>1</sup> gratuitos e pré-visualização SKUs não são fornecidos com SLA. SLA é impostas assim que um SKU torna-se ficará disponível.

<sup>2</sup> S3 e S3 HD são cópias por infraestrutura de elevada capacidade idênticos, mas cada uma delas atinge o seu limite máximo de diferentes formas. S3 destina-se um número mais pequeno de índices muito grandes. Como tal, o limite máximo está recurso vinculadas (2,4 TB para cada serviço). S3 HD destina-se um grande número de índices muito pequenos. Em índices de 1.000, S3 HD atinge os seus limites sob a forma de restrições de índice. Se for um cliente do S3 HD que necessite de mais de 1.000 índices, contacte o Microsoft Support para obter informações sobre como continuar.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminar SKUs não cumprem requisitos 

As perguntas seguintes podem ajudá-lo chegar a decisão SKU direita para a sua carga de trabalho.

1. Possui requisitos do **Contrato de nível de serviço (SLA)** ? Restrinja a decisão SKU padrão básico ou não pré-visualização.
2. **Índices quantas** fazer requerem? Uma das variáveis maiores factoring para uma decisão SKU é o número de índices suportados pelo cada SKU. Suporte de índice remissivo está em nitidamente diferentes níveis as camadas comparar inferiores. Requisitos de número de índices poderão ser um primária determinante de uma decisão SKU.
3. **Quantos documentos** vão ser carregados para cada índice? O número e o tamanho dos documentos irão determinar o tamanho eventual do índice. Partindo do princípio de que pode calcular o tamanho previsto do índice remissivo, pode compare esse número contra o tamanho da partição por SKU, expandido pelo número de partições necessários para armazenar um índice remissivo desse tamanho. 
4. **O que é a carga de consulta esperado**? Assim que sejam compreendidos requisitos de armazenamento, considere cargas de trabalho de consulta. S2 e ambos os SKUs S3 oferecem débito perto equivalente, mas os requisitos de SLA irão excluir qualquer SKUs de pré-visualização. 
5. Se estiver a considerar a camada S2 ou S3, determine se necessita [indexadores](search-indexer-overview.md). Indexadores ainda não estão disponíveis para a camada S3 HD. Abordagem alternativa consiste em utilizar um modelo de push existência de actualizações de índice remissivo, onde escreve o código da aplicação para transmitir um conjunto de dados para um índice remissivo.

A maioria dos clientes pode regra um SKU específica ou reduzir com base nas suas respostas às perguntas acima. Se ainda não tem a certeza qual SKU para acompanhar, pode publique perguntas nos fóruns do MSDN ou StackOverflow ou contacte o suporte do Azure para obter orientações adicionais.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Decisão validação: é que o SKU oferecem armazenamento suficiente e QPS?

Como um último passo, revisitar [preços de página](https://azure.microsoft.com/pricing/details/search/) e as [secções por serviço e por índice no serviço de limites](search-limits-quotas-capacity.md) para verifique novamente se as estimativas de limites de subscrição e serviço. 

Se os requisitos do preço ou armazenamento estiverem fora dos limites, poderá pretender reestruturar as cargas de trabalho entre vários serviços mais pequenos (por exemplo). No nível mais granular, poderia índices para ser mais pequeno de reestruturar as ou utilizar filtros para tornar as consultas mais eficientes.

> [AZURE.NOTE] Requisitos de armazenamento podem ser indevidamente insuflados se documentos contêm dados estranhos. Idealmente, os documentos contêm apenas dados pesquisáveis ou metadados. Dados binários são que não sejam pesquisável e deverão ser armazenados separadamente (talvez em armazenamento Azure de tabela ou blob) com um campo no índice remissivo para colocar em espera uma referência de URL aos dados externos. O tamanho máximo de um documento individual é 16 MB (ou menos se estiver em volume ao carregar vários documentos no um pedido). Para mais informações, consulte [limites de serviço do Azure pesquisa](search-limits-quotas-capacity.md) .

## <a name="next-step"></a>Passo seguinte

Quando já souber que SKU é o ajuste à direita, continue com estes passos:

- [Criar um serviço de pesquisa no portal](search-create-service-portal.md)
- [Alterar a atribuição de partições e réplicas para dimensionar o seu serviço](search-capacity-planning.md)

