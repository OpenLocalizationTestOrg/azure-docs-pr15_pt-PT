<properties
    pageTitle="Modelação Multitenancy na pesquisa Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Saiba como padrões de design comuns para multi-inquilino SaaS aplicações enquanto utilizando a pesquisa do Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Estruturar padrões para multi-inquilino SaaS aplicações e pesquisa do Azure

Uma aplicação multi-inquilino é um valor que fornece o mesmo serviços e funcionalidades para qualquer número de inquilinos quem não é possível ver ou partilhar os dados de qualquer outro inquilino. Este documento aborda estratégias de isolamento de inquilino para multi-inquilino aplicações criadas com pesquisa Azure.

## <a name="azure-search-concepts"></a>Azure conceitos de pesquisa
Como uma solução de pesquisa-como-a-service Azure pesquisa permite que os programadores adicionar experiências de pesquisa avançada às aplicações sem gerir qualquer infraestrutura ou tornar-se um especialista na pesquisa. Dados são carregados para o serviço e, em seguida, armazenados na nuvem. Utilizar pedidos simples à API de pesquisa do Azure, os dados podem, em seguida, ser modificados e procurados. Pode encontrar uma descrição geral do serviço de [neste](http://aka.ms/whatisazsearch)artigo. Antes de debater padrões de design, é importante compreender alguns conceitos na pesquisa Azure.

### <a name="search-services-indexes-fields-and-documents"></a>Serviços de pesquisa, índices, campos e documentos
Ao utilizar a pesquisa do Azure, um subscreve a um _serviço de pesquisa_. Como os dados são carregados para pesquisa Azure, são armazenado num _índice_ dentro do serviço de pesquisa. Pode ser um número de índices dentro de um único serviço. Para utilizar os conceitos familiares das bases de dados, o serviço de pesquisa pode ser likened para uma base de dados enquanto os índices dentro de um serviço podem ser likened para tabelas dentro de uma base de dados.

Cada índice de um serviço de pesquisa tem o seu próprio esquema, o que é definida por um número de personalizáveis _campos_. Dados são adicionados a um índice de pesquisa do Azure sob a forma de individuais _documentos_. Cada documento tem de ser carregado para um determinado índice e tem de se ajustar esquema do índice remissivo. Quando procurar dados utilizando a pesquisa do Azure, as consultas de pesquisa de texto completo são emitidas contra um nomeadamente índice.  Para comparar estes conceitos de uma base de dados, podem ser likened campos para as colunas numa tabela e podem ser likened documentos para linhas.

### <a name="scalability"></a>Escalabilidade
Qualquer serviço de procura do Azure padrão [preços camada](https://azure.microsoft.com/pricing/details/search/) pode dimensionar em duas dimensões: armazenamento e disponibilidade.
* Podem ser adicionados a _partições_ para aumentar o armazenamento de um serviço de pesquisa.
* _Réplicas_ podem ser adicionados a um serviço para aumentar o débito de pedidos de um serviço de pesquisa pode processar.

Adicionar e remover a partições e a réplicas na permitirá a capacidade do serviço de pesquisa para crescer com a quantidade de dados e os pedidos de aplicação de tráfego. Ordem de um serviço de pesquisa para alcançar uma leitura [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), requer duas réplicas. Ordem de um serviço para alcançar um de leitura / escrita [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), requer três réplicas.


### <a name="service-and-index-limits-in-azure-search"></a>Limites de serviço e índice remissivo na pesquisa do Azure
Não existem diferente do alguns [preços camadas](https://azure.microsoft.com/pricing/details/search/) na pesquisa Azure, cada uma das camadas tem diferentes [dos limites e as quotas de](search-limits-quotas-capacity.md). Algumas destes limites são ao nível do serviço, algumas são ao nível do índice remissivo e algumas são ao nível do partição.


|                                  | Básicas     | Standard1   | Standard2   | Standard3   | Standard3 HD  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Máximas réplicas por serviço     | 3         | 12          | 12          | 12          | 12            |
| Máximas partições por serviço   | 1         | 12          | 12          | 12          | 1             |
| Pesquisa máximo de unidades (réplicas * partições) por serviço | 3         | 36          | 36          | 36          | 36 (máximos 3 partições)            |
| Máximo de documentos por serviço    | 1 milhão | milhões de 180 | milhões de 720 | mil milhões de 1.4 | 600 milhões   |
| Máximo de armazenamento por serviço      | 2 GB      | 300 GB      | 1.2 TB      | 2.4 TB      | 600 GB        |
| Máximo de documentos por partição  | 1 milhão | 15 milhões  | milhões de 60  | 120 milhões de | 200 milhões   |
| Máximo de armazenamento por partição    | 2 GB      | 25 GB       | 100 GB      | 200 GB      | 200 GB        |
| Índices máximos por serviço      | 5         | 50          | 200         | 200         | 3000 (máx 1000 índices/partição)          |


#### <a name="s3-high-density"></a>S3 Alta densidade '
Na camada de preços S3 do Azure pesquisa, existe uma opção para o modo de alto densidade (HD) concebido especificamente para multi-inquilino cenários. Em muitos casos, é necessário suportar um grande número de inquilinos mais pequenos num único serviço para atingir os benefícios da eficiência simplificar e o custo.

S3 HD permite que os índices muitos pequenos ser embalado em gestão de um serviço de pesquisa única por negociação a capacidade de dimensionar saída índices utilizando a partições a capacidade de para alojar índices mais num único serviço.

Concretamente, poderia ter um serviço de S3 entre 1 e 200 índices que em conjunto, podem alojar até 1.4 mil milhões de documentos. Um HD S3 outro lado permitirá índices individuais aceder apenas até 1 milhão de documentos, mas pode processar até 1000 índices por partição (até 3000 por serviço) com uma contagem de documento total de 200 milhões por partição (até 600 milhão de serviço).



## <a name="considerations-for-multitenant-applications"></a>Considerações para aplicações multi-inquilino
Aplicações multi-inquilino eficazmente tem de distribuir recursos entre inquilinos preservando algum nível de privacidade entre inquilinos vários. Existem algumas considerações ao estruturar a arquitetura para uma aplicação como:

* _Isolamento de inquilinos:_ Precisa de tomar medidas adequadas para assegurar que os inquilinos sem não autorizado ou indesejados acesso aos dados de outros inquilinos os programadores de aplicação. Para além da perspetiva de privacidade de dados, estratégias de isolamento de inquilino requerem uma gestão eficaz de recursos partilhados e proteção contra vizinhos ruídos.
* _Na nuvem custo de recursos:_ Tal como acontece com qualquer outra aplicação soluções de software tem de permanecer custo competitivo como um componente de uma aplicação de multi-inquilino.
* _Facilidade de operações:_ Quando desenvolver uma arquitetura de multi-inquilino, o impacto na operações e complexidade a aplicação é uma consideração importante. Pesquisa Azure tem um [SLA 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Ambiental global:_ Aplicações multitenant, poderão ter de eficazmente servir inquilinos que são distribuídos ao longo de globo.
* _Escalabilidade:_ Os programadores de aplicações necessitam considerar como de reconciliar entre manter um nível suficientemente baixo de complexidade da aplicação e a estruturar a aplicação para dimensionar com o tamanho de dados e a carga de trabalho dos inquilinos e número de inquilinos.

Pesquisa Azure oferece alguns limites que podem ser utilizados para identificar dos inquilinos dados e carga de trabalho.

## <a name="modeling-multitenancy-with-azure-search"></a>Modelação multitenancy com a pesquisa do Azure
No caso de um cenário multi-inquilino, o programador da aplicação consome um ou mais serviços de pesquisa e dividir os respetivos inquilinos entre serviços, índices ou ambas. Pesquisa Azure tem alguns padrões comuns quando modelação um cenário multi-inquilino:

1. _Índice remissivo por inquilino:_ Cada inquilino tem as suas próprias índice dentro de um serviço de pesquisa que é partilhado com outros inquilinos.
1. _Serviço por inquilino:_ Cada inquilino tem os suas próprias dedicado serviço de pesquisa do Azure, que oferece mais alto nível de separação de dados e a carga de trabalho.
1. _Mistura dos dois:_ Maiores e mais ativo inquilinos são atribuídos a serviços dedicados enquanto inquilinos mais pequenos são atribuídos índices individuais dentro de serviços partilhados.

## <a name="1-index-per-tenant"></a>1. indexar por inquilino
![Uma imagem do modelo de índice remissivo por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Um modelo de índice remissivo por inquilino, múltiplos inquilinos do ocupam um único serviço de pesquisa do Azure onde cada inquilino tem os seus próprios índice.

Inquilinos alcançar isolamento de dados, uma vez que todos os pedidos de pesquisa e operações de documento são enviadas a um nível de índice remissivo no Azure pesquisa. Na camada de aplicação, existe a necessidade de detecção para direcionar tráfego de inquilinos vários para os índices inicial enquanto também gerir os recursos ao nível do serviço em todos os inquilinos.

Um atributo-chave do modelo de índice remissivo por inquilino é a capacidade para o Programador de aplicação para oversubscribe a capacidade de um serviço de pesquisa entre inquilinos a aplicação. Se a inquilinos tem uma distribuição desigual de carga de trabalho, a combinação ideal de inquilinos pode ser distribuída por índices de um serviço de pesquisa para acomodar um número de inquilinos altamente ativos, consumir muitos recursos enquanto funcionar em simultâneo uma longa Cauda de inquilinos menos ativos. O compromisso está a impossibilidade do modelo de lidar com situações em que cada inquilino em simultâneo é altamente ativo.

O modelo de índice remissivo por inquilino fornece a base para um modelo de custo variável, onde um serviço de pesquisa do Azure inteiro é comprado inicial e, em seguida subsequentemente preenchido com os inquilinos. Esta opção permite-não utilizadas capacidade a ser designado para as versões de avaliação e contas gratuitas.

Para as aplicações com um ambiental global, o modelo de índice remissivo por inquilino pode não ser mais eficiente. Se a inquilinos de uma aplicação estão distribuídos o globo, um serviço em separado poderá ser necessário para cada região que pode duplicar custos ao longo de cada um deles.

Azure pesquisa permite a escala dos índices individuais e o número total de índices para aumentar. Se a preços adequado camada é escolhido, partições e réplicas podem ser adicionadas ao serviço de procura inteira quando um índice remissivo individual dentro do serviço for demasiado grande em termos de tráfego de armazenamento ou.

Se o número total de índices ficar demasiado grande para um único serviço, outro serviço de tem de ser aprovisionada para acomodar os novos inquilinos. Se tiverem índices ser movida entre os serviços de pesquisa são adicionados novos serviços, os dados do índice tem de ser copiados manualmente a partir de um índice para a outra como Azure pesquisa não permite a um índice ser movida.


## <a name="2-service-per-tenant"></a>2. serviço por inquilino
![Uma imagem do modelo de serviço por inquilino](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Numa arquitetura de serviço por inquilino, cada inquilino tem os suas próprias serviço de pesquisa.

Neste modelo, a aplicação atinge o máximo nível de isolamento aos seus inquilinos. Cada serviço tem dedicado armazenamento e débito para processamento de pedido de pesquisa, bem como chaves API separadas.

Para as aplicações onde cada inquilino tem uma grande menores ou a carga de trabalho variabilidade pequeno a partir de um inquilino para o inquilino, o modelo de serviço por inquilino é uma escolha efectiva como recursos não são partilhados ao longo das cargas de trabalho dos vários inquilinos.

Um serviço por modelo de inquilino oferece também o benefício de um modelo de custo fixo, previsíveis. Não existe nenhum investimento inicial num serviço de pesquisa inteira até haver um inquilino para preenchê-lo, no entanto, o custo por inquilino é superior a um modelo de índice remissivo por inquilino.

O modelo de serviço por inquilino é uma escolha eficaz para as aplicações com um ambiental global. Com os inquilinos distribuídos geograficamente, é fácil para que o serviço de cada inquilino na região adequado.

Os desafios no dimensionamento este padrão surgirem quando inquilinos individuais outgrow dos seus serviços. Pesquisa Azure não suporta atualmente atualizar a camada preços de um serviço de pesquisa, para que todos os dados teria manualmente sejam copiados para um novo serviço.

## <a name="3-mixing-both-models"></a>3. Misturar ambos os modelos
Outro padrão para modelação multitenancy é misturar estratégias de índice remissivo por inquilino e serviço por inquilino.

Por misturar os padrões de dois, inquilinos maior uma aplicação podem ocupar serviços dedicados enquanto cauda longa da inquilinos menos ativos, mais pequenos pode ocupar índices num serviço partilhado. Este modelo assegura que os inquilinos maior têm forma consistente alto desempenho de serviço enquanto ajudando-o a proteger inquilinos mais pequenos de qualquer vizinhos ruídos.

No entanto, esta estratégia de execução baseia-se prospectiva na previsão para os quais inquilinos irão necessitar de um serviço dedicado versus um índice remissivo num serviço partilhado. Aumenta complexidade da aplicação com a necessidade de gerir ambos estes modelos multitenancy.

## <a name="achieving-even-finer-granularity"></a>Alcançar o mesmo melhor granularidade
Os padrões de estrutura acima para modelar cenários multi-inquilino na pesquisa Azure partem do pressuposto um âmbito uniforme onde cada inquilino é uma instância inteira de uma aplicação. No entanto, aplicações, por vezes, podem processar muitos âmbitos mais pequenos.

Se os modelos de serviço por inquilino e índice remissivo por inquilino não estiverem âmbitos suficientemente pequenos, é possível um índice remissivo para alcançar um grau ainda mais preciso de granularidade do modelo.

Para que um único índice remissivo se comportam de forma diferente para os pontos finais de cliente diferente, um campo pode ser adicionado a um índice remissivo que designa um determinado valor para cada cliente possível. Sempre que um cliente chamadas Azure pesquisa de consulta ou modificar um índice remissivo, o código da aplicação cliente especifica o valor apropriado para esse campo utilizando a funcionalidade de [filtro](https://msdn.microsoft.com/library/azure/dn798921.aspx) de pesquisa do Azure no momento da consulta.

Este método podem ser utilizados para alcançar a funcionalidade de contas de utilizador em separado, níveis de permissões separados e ainda completamente separar aplicações.

## <a name="next-steps"></a>Próximos passos
Pesquisa Azure é uma escolha apelativa para muitas aplicações, [Saiba mais sobre as capacidades de robusta o serviço](http://aka.ms/whatisazsearch). Ao avaliar os padrões de estrutura diversos para aplicações multi-inquilino, considere as [várias camadas comparar](https://azure.microsoft.com/pricing/details/search/) e os respetivos [limites de serviço](search-limits-quotas-capacity.md) para melhor adaptar Azure pesquisa para se ajustar cargas de trabalho de aplicação e arquitecturas de todos os tamanhos.

Qualquer perguntas acerca de pesquisa do Azure e cenários multitenant, podem ser direccionadas para azuresearch_contact@microsoft.com.
