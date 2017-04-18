<properties
   pageTitle="Estruturar padrões para multi-inquilino SaaS aplicações e base de dados do SQL Azure | Microsoft Azure"
   description="Este artigo aborda os requisitos e comuns padrões de arquitectura de dados da base de dados multi-inquilino aplicações em execução num ambiente nuvem necessitam ter em consideração e as responsabilidades vários associadas com estes padrões. Também explica como base de dados SQL Azure, com as respetivas flexível agrupamentos e flexível ferramentas, ajudar a solucionar estes requisitos de uma forma não compromisso."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>Estruturar padrões para multi-inquilino SaaS aplicações e base de dados do SQL Azure

Neste artigo, pode obter informações sobre os requisitos e dados comuns padrões de arquitectura do software multitenant, como um aplicações de base de dados de serviço (SaaS) que são executadas num ambiente na nuvem. Também explica os fatores que é necessário considerar e os compromissos de padrões de estrutura diferente. Conjuntos de dados flexível e flexível ferramentas de base de dados do SQL Azure podem ajudá-lo aos seus requisitos específicos sem comprometer a outros objectivos.

Os programadores fazer, por vezes, escolhas que funcionam com os seus interesses melhor a longo prazo quando estrutura de modelos de arrendamento para as camadas de dados das aplicações multi-inquilino. Inicialmente, pelo menos, um programador pode interpretam facilidade de desenvolvimento e inferiores custos de fornecedor de serviço de nuvem como mais importante do que inquilino isolamento ou escalabilidade de uma aplicação. Esta opção pode levar a preocupações satisfação dos clientes e uma correcção de curso dispendiosa mais tarde.

Uma aplicação multi-inquilino é uma aplicação alojada num ambiente na nuvem e que fornece o mesmo conjunto de serviços para centenas ou milhares de inquilinos quem não partilhar ou consulte o artigo uns dos outros dados. Um exemplo é uma aplicação de SaaS que fornece serviços a inquilinos num ambiente alojado na nuvem.

## <a name="multitenant-applications"></a>Aplicações multi-inquilino

Nas aplicações multi-inquilino, dados e a carga de trabalho podem ser facilmente divididos. Qual pode criar partições dados e a carga de trabalho, por exemplo, ao longo de limites de inquilino, uma vez que a maioria dos pedidos ocorrerem dentro dos limites de um inquilino. Esta propriedade está inerente os dados e a carga de trabalho e preferência-la pelos padrões de aplicação outros fabricantes referidos neste artigo.

Os programadores utilizar este tipo de aplicação ao longo de todo o espectro de aplicações baseadas na nuvem, incluindo:

- Aplicações de base de dados do parceiro que estão a ser que transitaram na nuvem como SaaS aplicações
- SaaS aplicações criadas para a nuvem a partir do terreno para cima
- Aplicações directas, destinado do cliente
- Aplicações empresariais de empregado destinado

SaaS as aplicações que foram concebidas para a nuvem e aqueles com raízes como normalmente do parceiro aplicações de base de dados são multi-inquilino aplicações. Estas aplicações SaaS entregar uma aplicação de software especializado como um serviço a suas inquilinos. Inquilinos podem aceder ao serviço de aplicação e tenha completo que é o proprietário dos dados associados armazenados como parte da aplicação. Mas, para tirar partido dos benefícios dos SaaS, os inquilinos tem entrega algum controlo sobre os seus próprios dados. O fornecedor de serviços de SaaS para manter os respetivos dados de seguros e isolados a partir de dados dos outros inquilinos ou fidedignos. Exemplos deste tipo de aplicação de SaaS multi-inquilino são MYOB, SnelStart e Salesforce.com. Cada um destas aplicações pode ser dividida ao longo dos limites do inquilino e suporte a aplicação de estruturar padrões discutimos neste artigo.

As aplicações que forneça um serviço direto para os clientes ou para funcionários dentro de uma organização (muitas vezes, os utilizadores, em vez de inquilinos) são outra categoria sobre o espectro de aplicação multi-inquilino. Os clientes subscrever o serviço e não é o proprietário os dados que o fornecedor de serviços recolhe e armazena. Fornecedores de serviços têm reduzidos requisitos para manter os dados dos seus clientes, isolados umas das outras, para além dos regulamentos de privacidade impostas aplicadas administração pública. Exemplos deste tipo de cliente destinado multi-inquilino aplicação são fornecedores de conteúdo de multimédia, como Netflix, Spotify e Xbox LIVE. Outros exemplos de aplicações facilmente partitionable são destinado ao cliente, aplicações de escala de Internet, ou aplicações de Internet coisas (IoT) no qual cada podem dispositivo ou cliente servem uma partição. Limites do partição podem separar os utilizadores e dispositivos.

Nem todas as partição de aplicações facilmente ao longo de uma única propriedade como inquilino, o cliente, o utilizador ou o dispositivo. Por exemplo, um recurso de empresa complexa planeamento da aplicação (ERP), tem produtos, encomendas e clientes. -Normalmente possui um esquema complexo com milhares de tabelas altamente interligados.

Sem estratégia única partição pode aplicar a todas as tabelas e trabalhar em toda a carga de trabalho de uma aplicação. Este artigo foca-se em aplicações multi-inquilino que têm dados facilmente partitionable e das cargas de trabalho.

## <a name="multitenant-application-design-trade-offs"></a>Aplicação multi-inquilino estrutura compromissos

O padrão de estrutura que um programador de aplicação multi-inquilino escolhe normalmente baseia-se uma consideração os seguintes elementos:

-   **Isolamento de inquilino**. O programador precisa para se certificar de que nenhuma inquilino tem indesejado acesso aos dados dos outros inquilinos. O requisito de isolamento expande para outras propriedades, tais como protegendo vizinhos ruídos, ser capaz de restaurar os dados de um inquilino e personalizações específicas do inquilino de execução.
-   **Custo de recursos na nuvem**. Uma aplicação de SaaS tem de estar custo competitiva. Um programador de aplicação multitenant, poderá optar por otimizar para custo inferior a utilização de recursos de nuvem, como o armazenamento e calcular os custos.
-   **Facilidade de DevOps**. Um programador de aplicação multitenant, tem de incorporar proteção de isolamento, manter e monitorizar o estado de funcionamento da sua aplicação e o esquema da base de dados e resolução de problemas de inquilino. Complexidade no desenvolvimento de aplicações e operação traduz diretamente para aumento do custo e pede com satisfação do inquilino.
-   **Escalabilidade**. A capacidade de adicionar incrementada inquilinos mais e a capacidade de inquilinos que necessitam da mesma é obrigatório para uma operação de SaaS com êxito.

Cada um dos seguintes fatores tem compromissos comparadas comparados o outro. Nuvem mais baixos custo perguntar se não poderá proporcionam a experiência de desenvolvimento mais prática. É importante para que um programador fazer escolhas informadas sobre estas opções e os seus compromissos durante o processo de estrutura da aplicação.

Um padrão de desenvolvimento populares é pack múltiplos inquilinos numa ou algumas bases de dados. Os benefícios desta abordagem são um custo inferior porque pagar para alguns bases de dados e a simplificar relativa de trabalhar com um número limitado de bases de dados. Mas ao longo do tempo, um programador de aplicação multi-inquilino SaaS irá aperceba que esta opção tem downsides substanciais no inquilino isolamento e escalabilidade. Se o inquilino isolamento torna-se importante, esforço adicional é necessária para proteger os dados de inquilino no armazenamento partilhado a partir do acesso não autorizado ou vizinhos ruídos. Este esforço adicional significativamente poderá impulsionar esforços de desenvolvimento e os custos de manutenção de isolamento. Da mesma forma, se for necessário adicionar inquilinos, este padrão de estrutura normalmente requer conhecimentos a redistribuir dados do inquilino através de bases de dados para corretamente dimensionar a camada de dados de uma aplicação.  

Muitas vezes isolamento de inquilino é um requisito fundamental nas aplicações de multi-inquilino SaaS que satisfazer para empresas e organizações. Um programador pode ser tentado por vantagens estimadas no simplificar e custo isolamento de inquilino e escalabilidade. Este compromisso pode provar complexo e dispendioso como o serviço cresce e requisitos de isolamento de inquilino tornam-se mais importantes e geridos na camada de aplicação. No entanto, multi-inquilino aplicações que forneça um serviço direto, consumidor destinado aos clientes, isolamento de inquilino poderá ser uma prioridade mais baixa que otimizar para o custo de recursos na nuvem.

## <a name="multitenant-data-models"></a>Modelos de dados multi-inquilino

Práticas de estrutura comuns para colocar dados do inquilino siga três modelos distintos, apresentados na figura 1.


  ![Modelos de dados de aplicação multi-inquilino](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
 figura 1: comuns práticas de estrutura para modelos de dados multi-inquilino

-   **Base de dados por inquilino**. Cada inquilino tem a sua própria base de dados. Todos os dados específicos do inquilino é confinados à base de dados do inquilino e isolados a partir de outras inquilinos e respetivos dados.
-   **Base de dados sharded partilhado**. Múltiplos inquilinos do partilham uma das várias bases de dados. Um conjunto distinto de inquilinos está atribuído a cada base de dados utilizando uma estratégia de criação de partições como hash, intervalo ou a partições de lista. Esta estratégia de distribuição de dados com frequência é referida como sharding.
-   **Partilhado da base de dados-single**. Uma base de dados simples, por vezes grande, contém dados para todos os inquilinos, que são desambiguados numa coluna de ID do inquilino.

> [AZURE.NOTE] Um programador de aplicações poderá optar por colocar inquilinos diferentes esquemas de base de dados diferente e, em seguida, utilize o nome do esquema para disambiguate inquilinos diferentes. Recomendamos que não esta abordagem porque requer a utilização de SQL dinâmico normalmente e não podem ser eficaz no plano colocação em cache. O restante deste artigo, iremos concentrar-na abordagem de tabela partilhada para esta categoria de aplicação multi-inquilino.

## <a name="popular-multitenant-data-models"></a>Modelos de dados multi-inquilino populares

É importante avaliar os diferentes tipos de modelos de dados multi-inquilino em termos de aplicação estrutura compromissos que já identificámos. Seguintes fatores ajudam caracterizar os três modelos de dados multi-inquilino mais comuns descritos anteriormente e os respetivos a utilização da base de dados, conforme apresentado na figura 2.

-   **Isolamento**. O grau de isolamento entre inquilinos pode ser uma medida de isolamento quanto inquilino atinja um modelo de dados.
-   **Custo de recursos na nuvem**. A quantidade de partilha de recursos entre inquilinos pode otimizar o custo de recursos na nuvem. Um recurso pode ser definido como o custo cluster e armazenamento.
-   **Custo DevOps**. Facilidade de desenvolvimento de aplicações, implementação e gestão reduz o custo de operação SaaS geral.  

Figura 2, o eixo dos YY mostra o nível de isolamento de inquilino. O eixo X mostra o nível de partilha de recursos. Cinzento, seta diagonal no meio indica a direção de custos DevOps, destinadas aumentar ou diminuir.

![Padrões de estrutura da aplicação multi-inquilino populares](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) figura 2: modelos de dados multi-inquilino populares

No canto inferior direito Quadrante na figura 2 mostra um padrão de aplicação que utiliza uma potencialmente grande, base de dados única partilhada e a tabela partilhada (ou esquema separado) abordagem. É boa ideia para partilha uma vez que todos os inquilinos utilizam os mesmos recursos de base de dados (CPU, memória, entrada/saída) numa única base de dados de recursos. Mas isolamento de inquilino é limitado. Poderá ter de efetuar passos adicionais para proteger inquilinos umas das outras na camada de aplicação. Estes passos adicionais podem aumentar significativamente o custo de DevOps de desenvolver e gerir a aplicação. Escalabilidade é limitada pela escala do hardware que aloja a base de dados.

No canto inferior esquerdo Quadrante na figura 2 ilustra múltiplos inquilinos do sharded em várias bases de dados (normalmente, hardware diferentes Dimensionar unidades). Cada base de dados aloja um subconjunto de inquilinos, que do interesse escalabilidade da outras padrões de endereços. Se for necessária para o mais inquilinos mais capacidade, pode facilmente colocar inquilinos no atribuídos para novas unidades de escala de hardware novas bases de dados. No entanto, é reduzida a quantidade de partilha de recursos. Apenas os inquilinos colocados sobre as mesmas unidades de escala partilhar recursos. Esta abordagem fornece pequeno melhoramento do inquilino isolamento porque muitos inquilinos ainda estão co-instalados sem automaticamente a ser protegidos a partir de outras ações. Complexidade da aplicação permanece alta.

No canto superior esquerdo Quadrante na figura 2 é a abordagem de terceira. Coloca a dados de cada inquilino na sua própria base de dados. Esta abordagem tem propriedades de boa de isolamento de inquilino mas limita a partilha de recursos quando cada base de dados tem os suas próprias dedicados recursos. Esta abordagem é boa se tem o inquilinos todas as cargas de trabalho previsíveis. Se menos previsíveis inquilino cargas de trabalho, o fornecedor não pode otimizar a partilha de recursos. Unpredictability é comum para SaaS aplicações. O fornecedor tem em aprovisionar excessiva para satisfazer necessidades ou recursos inferiores. Um dos resultados de ação em custos superiores ou inferior satisfação do inquilino. Um maior grau de recurso que partilha através de inquilinos torna-se conveniente para tornar a solução mais rentável. Também aumentar o número de bases de dados aumenta o custo de DevOps para implementar e manter a aplicação. Apesar destas questões, este método fornece o isolamento melhor e mais fácil para os inquilinos.

Seguintes fatores também influenciam o padrão de estrutura que seleciona um cliente:

-   **A propriedade de dados do inquilino**. Uma aplicação em que os inquilinos reter que é o proprietário dos seus próprios dados preferência pelo padrão de uma base de dados única por inquilino.
-   **Escala**. Uma aplicação que destinos centenas de milhares ou milhões de inquilinos preferência por partilha abordagens como sharding de base de dados. Requisitos de isolamento ainda podem representar desafios.
-   **Valor e business Modeling**. Se uma aplicação por-inquilino receita se pequenas (menos de dólar), os requisitos de isolamento tornar menos críticos e uma base de dados partilhada faz sentido. Se for receita por inquilino euros alguns ou mais, um modelo de base de dados por inquilino é mais viável. Podem ajudar a reduzir os custos de desenvolvimento.

Um modelo de multi-inquilino ideal dada os estrutura compromissos apresentados na figura 2, tem de incorporar propriedades de isolamento de inquilino boa com recursos ideal partilha entre inquilinos. Este modelo ajusta-se na categoria descrita no canto superior direito quadrante do figura 2.

## <a name="multitenancy-support-in-azure-sql-database"></a>Suporte multitenancy base de dados do SQL Azure

Base de dados SQL Azure suporta todos os padrões de aplicação multi-inquilino descritos na figura 2. Com os agrupamentos flexível, também suporta um padrão de aplicação que combina a partilha de recursos bom e os benefícios de isolamento da base de dados por inquilino abordagem (consulte o canto superior direito Quadrante na figura 3). Ferramentas de base de dados flexível e capacidades de base de dados SQL ajudam a reduzir o custo para desenvolver e trabalhar com uma aplicação que tem muitas bases de dados (mostrados na área sombreada na figura 3). Estas ferramentas podem ajudar a criar e gerir as aplicações que utilizam um dos padrões de base de dados com várias.

![Padrões de base de dados do SQL Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) figura 3: padrões de aplicação multi-inquilino base de dados do SQL Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modelo de base de dados por inquilino com agrupamentos flexível e ferramentas

Flexível agrupamentos de base de dados SQL combinam isolamento de inquilino com recurso que partilha entre bases de dados do inquilino para melhor suportar a abordagem de base de dados por inquilino. Base de dados SQL é uma solução de camada de dados para fornecedores de SaaS quem multi-inquilino aplicações. Muda a carga de recurso que partilha entre inquilinos da camada de aplicação para a camada de serviço de base de dados. A complexidade da gestão e consultar à escala através de bases de dados é simplificada com tarefas flexível, flexível consulta, transações flexível e a biblioteca do cliente flexível da base de dados.

| Requisitos da aplicação | Capacidades de base de dados SQL |
| ------------------------ | ------------------------- |
| Isolamento de inquilino e partilha de recursos | [Conjuntos de dados flexível](sql-database-elastic-pool.md): atribuir um conjunto de recursos base de dados SQL e partilhar os recursos em diferentes bases de dados. Além disso, bases de dados individuais podem desenhar quanto recursos do conjunto, conforme necessário para acomodar capacidade pedido picos devido a alterações das cargas de trabalho do inquilino. O próprio conjunto de flexível pode ser dimensionado para cima ou para baixo, conforme necessário. Conjuntos de dados flexível também fornecem facilidade de capacidade de gestão e monitorização e resolução de problemas ao nível de agrupamento. |
| Facilidade de DevOps através de bases de dados | [Conjuntos de dados flexível](sql-database-elastic-pool.md): conforme indicado anteriormente.|
||[Consulta flexível](sql-database-elastic-query-horizontal-partitioning.md): consulta ao longo de bases de dados para elaboração de relatórios ou cruz-inquilino análise.|
||[Tarefas de flexível](sql-database-elastic-jobs-overview.md): pacote e implementar o sujeito operações de manutenção da base de dados ou alterações de esquema da base de dados em várias bases de dados.|
||[Transações flexível](sql-database-elastic-transactions-overview.md): processo altera a várias bases de dados de uma forma Atómica e isolada. Transações flexível são necessários quando precisam de aplicações "todos ou nada" garantias sobre as várias operações de base de dados. |
||[Biblioteca de cliente da base de dados flexível](sql-database-elastic-database-client-library.md): gerir as distribuições de dados e mapear a inquilinos para bases de dados. |

## <a name="shared-models"></a>Modelos partilhados

Conforme descrito acima, para a maioria dos fornecedores de SaaS, uma abordagem de modelo partilhado constituir problemas com problemas de isolamento de inquilino e complexidade de desenvolvimento de aplicações e manutenção. No entanto, para as aplicações multi-inquilino que forneça um serviço diretamente para consumidores, requisitos de isolamento de inquilino podem não ser uma como alta prioridade como minimizar custo. Poderá poderão pack inquilinos numa ou mais bases de dados de alta densidade para reduzir os custos. Utilizar uma base de dados única ou várias bases de dados sharded os modelos de base de dados partilhada poderão resultar em eficiência adicional no custo de partilha e geral de recursos. Base de dados SQL Azure fornece algumas funcionalidades que ajudam os clientes construir isolamento para segurança melhorada e gestão à escala da camada de dados.

| Requisitos da aplicação | Capacidades de base de dados SQL |
| ------------------------ | ------------------------- |
| Funcionalidades de isolamento de segurança | [Segurança de nível de linha](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Esquema de base de dados](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilidade de DevOps através de bases de dados | [Consulta flexível](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Tarefas de flexível](sql-database-elastic-jobs-overview.md) |
|| [Transações flexível](sql-database-elastic-transactions-overview.md) |
|| [Biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md) |
|| [Base de dados flexível dividida e intercalar](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Resumo

Requisitos de isolamento de inquilino são importantes para a maioria das aplicações de multi-inquilino SaaS. A melhor opção para fornecer isolamento fortemente leans face a abordagem de base de dados por inquilino. As outras duas abordagens requerem investimentos em camadas de aplicação complexa que requerem funcionários de desenvolvimento qualificados para fornecer isolamento, o que aumenta significativamente o custo e o risco. Se os requisitos de isolamento não são contabilizados no início do desenvolvimento de serviço, pode ser ainda mais dispendioso nos modelos de dois primeiros reequipamento-los. As desvantagens principais associadas com o modelo de base de dados por inquilino relacionados com custos de recursos maior nuvem devido a partilha reduzida e manutenção e gerir muitas bases de dados. Os programadores de aplicações SaaS frequentemente dificuldades quando fazem estes compromissos.

Apesar de compromissos poderão ser principais barreiras com a maioria dos fornecedores de serviço de nuvem da base de dados, base de dados do SQL Azure elimina as barreiras com as respetivas flexível agrupamento e capacidades flexível bases de dados. Os programadores de SaaS podem combinar as características de isolamento de um modelo de base de dados por inquilino e otimizar as melhorias de capacidade de gestão de muitas bases de dados e de partilha de recursos utilizando agrupamentos de flexível e ferramentas associadas.

Fornecedores de aplicação multi-inquilino que tenham sem requisitos de isolamento de inquilino e quem pode pack inquilinos numa base de dados de alta densidade achar que dados partilhada modelos de resultado nos eficiência adicional na partilha de recursos e reduzir custos gerais. Ferramentas de base de dados flexível de base de dados SQL Azure, sharding bibliotecas e funcionalidades de segurança ajudam os fornecedores de SaaS criar e gerir aplicações multi-inquilino.

## <a name="next-steps"></a>Próximos passos

[Introdução às ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md) com uma aplicação de exemplo que mostra a mudança a biblioteca do cliente.

Crie um [dashboard personalizado do conjunto de dados flexível para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) com uma aplicação de exemplo que utiliza flexível conjuntos de dados para obter uma solução de base de dados rentável e dimensionáveis.

Utilize as ferramentas de base de dados do Azure SQL para [Migrar bases de dados existentes por escalar para fora](sql-database-elastic-convert-to-use-elastic-tools.md).

Ver o nosso tutorial sobre como [criar um agrupamento de flexível](sql-database-elastic-pool-create-portal.md).  

Saiba como [monitorizar e gerir um agrupamento de flexível](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Recursos adicionais

- [O que é um agrupamento de flexível Azure?](sql-database-elastic-pool.md)
- [Dimensionamento saída com a base de dados do SQL Azure](sql-database-elastic-scale-introduction.md)
- [Aplicações multi-inquilino com ferramentas de base de dados flexível e segurança de nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Autenticação nas aplicações multi-inquilino através do Azure Active Directory e OpenID ligar](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicação de inquéritos brinquedos](../guidance/guidance-multitenant-identity-tailspin.md)
- [Solução rápida é iniciado](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidade

Para as questões, localize-no [Fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Adicione um pedido de funcionalidade no [Fórum de comentários de base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).
