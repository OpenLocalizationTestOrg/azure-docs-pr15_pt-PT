<properties
   pageTitle="O que é armazém de dados do SQL Azure? | Microsoft Azure"
   description="Classe empresarial distribuído capaz de processar petabyte volumes de dados que não sejam relacionais e da base de dados. É primeiro nuvem armazém de dados a indústria com a aumentar, encolher e coloque o cursor em segundos."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/27/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;kevin"/>


# <a name="what-is-azure-sql-data-warehouse"></a>O que é armazém de dados do SQL Azure?

Armazém de dados SQL Azure é uma baseado na nuvem, escala de saída base de dados capaz de processar grandes volumes de dados, não relacionais e relacionais. Criado no nossa arquitetura de processamento previstos excederem consideravelmente paralelas (MPP), armazém de dados SQL pode processar sua carga de trabalho do enterprise.

Armazém de dados SQL:

- Combina a base de dados relacional do SQL Server com as capacidades de escala de saída do Azure na nuvem. Pode aumentar, diminuir, coloque o cursor ou retomar cluster em segundos. Guarde os custos por escalar para fora da CPU quando precisar dele e cortar novamente a utilização durante as horas que não sejam de pico.
- Tira partido de plataforma do Azure. É fácil implementar, mantidas de forma totalmente integrada e totalmente a falhas tolerância a falhas devido a back-ups automáticas.
- Completa ecossistema do SQL Server. Pode desenvolver com familiar SQL Server, de Transact-SQL (T SQL) e ferramentas.

Este artigo descreve as funcionalidades principais do armazém de dados SQL.

## <a name="massively-parallel-processing-architecture"></a>Arquitetura de processamento previstos excederem consideravelmente paralelas

Armazém de dados do SQL é que uma transformação previstos excederem consideravelmente paralela (MPP) distribuído sistema de base de dados. Dividindo dados e a capacidade de processamento através de vários nós, armazém de dados SQL pode oferecer escalabilidade enorme - extremidade para além de qualquer sistema único.  Nos bastidores, armazém de dados SQL numa ou páginas os seus dados através de vários armazenamento nada partilhado e unidades de processamento. Os dados são armazenados no armazenamento localmente redundante Premium e ligados para calcular nós de execução de consulta. Com esta arquitectura, armazém de dados SQL leva uma abordagem "divida e conquiste" para executar o cargas e consultas complexas. Pedidos de são recebidos pelo nó do controlo, otimizados e, em seguida, passados para os nós de cluster para fazer o seu trabalho em paralelo.

Ao combinar arquitetura MPP e capacidades de armazenamento Azure, pode armazém de dados SQL:

- Aumentar ou encolher independentemente da cluster de armazenamento.
- Aumentar ou encolher cluster sem mover dados.
- Capacidade de cluster de colocar em pausa enquanto mantiverem seu dados.
- Currículo calcular capacidade de aviso de um momento.

O diagrama seguinte mostra a arquitetura mais detalhadamente.

![Arquitetura de armazém de dados SQL][1]


**Nó de controlo:** O nó do controlo gere e optimiza consultas. É o front-end que interage com todas as ligações e aplicações. No armazém de dados SQL, o nó do controlo é recorrendo a base de dados SQL e ligar-se para o mesmo aspeto e por achar da mesma. Em superfície, o nó do controlo coordenadas todos os movimento de dados e o cálculo necessária para executar consultas paralelas distribuído dos seus dados. Quando submete uma consulta T SQL para armazém de dados SQL, o nó do controlo transforma-consultas separadas que são executadas em cada nó cluster em paralelo.

**Calcular nós:** Os nós de cluster servem power atrás armazém de dados SQL. Estes são bases de dados do SQL que armazenar os seus dados e processar a sua consulta. Quando adiciona dados, armazém de dados SQL distribui as linhas para os nós de cluster. Os nós de cluster são os colaboradores na área executar as consultas paralelas dos seus dados. Após a transformação, estes passam os resultados novamente para o nó do controlo. Para concluir a consulta, o nó do controlo é agregado os resultados e devolve o resultado final.

**Armazenamento:** Os dados são armazenados numa armazenamento de Blobs do Azure. Quando nós de cluster interagem com os seus dados, escrever e ler de armazenamento de BLOBs e diretamente para. Uma vez que armazenamento Azure expande transparente e largamente, armazém de dados SQL pode fazer o mesmo. Uma vez que cluster e armazenamento são independentes, armazém de dados SQL automaticamente pode dimensionar armazenamento separadamente a partir do dimensionamento cluster e vice versa. Armazenamento de Blobs do Azure também é completamente tolerância a falhas e simplifica o processo de cópia de segurança e restauro.

**Serviço de movimento de dados:** Serviço de movimento de dados (DMS) move dados entre os nós. DMS dá o acesso de nós de cluster aos dados de que necessitam para associações e agregações. DMS não é um serviço Azure. É um serviço do Windows que é executada ao lado de base de dados SQL em todos os nós. Uma vez que DMS é executado em segundo plano, não interage com o mesmo diretamente. No entanto, quando olha para planos de consulta, vai notar incluem algumas operações DMS uma vez que é necessário executar cada consulta em paralelo movimento de dados.


## <a name="optimized-for-data-warehouse-workloads"></a>Otimizado para dados armazém das cargas de trabalho

A abordagem MPP é assistido por um número de desempenho específicos otimizações, incluindo de armazenamento de dados:

- Uma otimizador de consulta distribuída e um conjunto de estatísticas complexas ao longo de todos os dados. O serviço utilizando as informações sobre o tamanho de dados e distribuição, é possível optimizar as consultas por avaliar o custo de operações de consulta distribuída específico.

- Algoritmos avançados e técnicas integrados o processo de movimento de dados para eficazmente mover dados entre computação recursos conforme necessário para executar a consulta. Estas operações de movimento dados foram criadas e todos os otimizações para o serviço de movimento dados ocorrem automaticamente.

- Agrupado **columnstore** índices por predefinição. Ao utilizar armazenamento baseado em coluna, armazém de dados SQL obtém os ganhos de compressão, 5 em média x sobre armazenamento orientada tradicional e, até 10 x ou mais ganhos de desempenho da consulta. Análise de consultas que precisa de pesquisa um grande número de linhas trabalho excelente em índices de columnstore.


## <a name="predictable-and-scalable-performance"></a>Desempenho previsível e dimensionável

Armazém de dados SQL separa armazenamento e cluster, que permite que cada um deles dimensionar de forma independente. Armazém de dados SQL pode simplesmente e rapidamente Dimensionar para adicionar recursos adicionais cluster em aviso de um momento. Completar esta é a utilização do armazenamento de Blobs do Azure. BLOBs prevêem não só estável, replicado armazenamento, mas também a infraestrutura de expansão fáceis custo baixa. Utilizar esta combinação de armazenamento na nuvem escala e cluster Azure, armazém de dados SQL permite-lhe efetuar o pagamento de desempenho da consulta e armazenamento quando precisar dele. Alterar a quantidade de cluster é tão simple como mover um controlo de deslize no portal do Azure para a esquerda ou direita ou, também pode ser agendado utilizando T-SQL e PowerShell.

Juntamente com a capacidade de totalmente controlar a quantidade de cluster independentemente de armazenamento, armazém de dados SQL permite-lhe totalmente interromper o seu armazém de dados, o que significa que não paga cluster quando não precisar dele. Enquanto mantém o seu armazenamento no local, todos os cluster é disponibilizada para o conjunto principal do Azure, poupando-lhe dinheiro. Quando for necessário, basta retomar a cluster os seus dados e têm calcular disponíveis para a sua carga de trabalho.

## <a name="data-warehouse-units"></a>Unidades de armazém de dados

Atribuição de recursos para o seu armazém de dados SQL é medida em unidades de armazém de dados (DWUs). DWUs são uma medida de recursos subjacentes como CPU, memória, IOPS, que são atribuídos para o seu armazém de dados SQL. Aumentar o número de DWUs aumenta recursos e o desempenho. Especificamente, DWUs ajudar a garantir que:

- É possível dimensionar o seu armazém de dados facilmente, sem se preocupar software ou hardware subjacente.

- Pode prever melhoria de desempenho para um nível de DWU antes de alterar o tamanho do seu armazém de dados.

- O hardware e software da sua instância subjacente, podem alterar ou mova sem afetar o desempenho do seu carga de trabalho.

- Microsoft pode fazer ajustes para a arquitetura subjacente do serviço de sem afetar o desempenho da sua carga de trabalho.

- Microsoft rapidamente pode melhorar o desempenho no armazém de dados do SQL, de forma que é dimensionáveis e uniformemente efeitos do sistema.

Unidades de dados de armazém fornecer uma medida de três métricas exatos que estão associados altamente desempenho de carga de trabalho de armazenamento de dados. O objetivo é que as seguintes métricas de chave carga de trabalho serão escala linear com os DWUs que escolheu para o seu armazém de dados.

**Digitalização/agregação:** Esta métrica carga de trabalho leva-o até um consulta que verifica um grande número de linhas e, em seguida, executa uma agregação complexa de armazenamento de dados padrão. Esta é uma operação intensa e/s e CPU.

**Carga:** Esta métrica medidas a capacidade de ingerir esta última dados no serviço. Cargas são preenchidas com PolyBase carregamento de um representante de conjunto de dados a partir do armazenamento de Blobs do Azure. Esta métrica foi concebida para aspetos da CPU do serviço e de rede de limite.

**Criar a tabela como selecionar (CTAS):** CTAS medidas a capacidade de copiar uma tabela. Isso envolve ler dados a partir do armazenamento, distribuir a em todos os nós do aparelho e escrever novamente ao armazenamento. É uma operação de intensa CPU, IO e de rede.

## <a name="pause-and-scale-on-demand"></a>Colocar em pausa e a escala a pedido

Quando precisar de resultados mais rapidamente, aumentar a sua DWUs e pagar para obter um desempenho maior. Quando precisar de menos calcular power, diminuir os DWUs e pagar apenas para o que precisa. Poderá pensar sobre como alterar o seu DWUs nestes cenários:

- Quando não tem de executar consultas, talvez a noites ou os fins de semana, colocar em repouso as suas consultas. Em seguida, coloque o cursor os recursos de cluster para evitar paga por DWUs quando não precisar deles.

- Quando o sistema tiver baixa procura, considere reduzir DWU para um tamanho pequeno. Pode ainda aceder aos dados, mas a reduções de custo significativa.

- Quando executar uma operação de carregamento ou transformação de dados muito carregado, poderá querer escalar para cima para que os dados estiverem disponíveis mais rapidamente.

Para compreender o que é o seu valor DWU ideal, experimente dimensionamento cima e para baixo e executar consultas de alguns depois de carregar os seus dados. Uma vez que dimensionamento rápida, que pode experimentar um número de níveis diferentes de desempenho numa hora ou menos.  Tenha em atenção, que armazém de dados SQL foi concebido para processar grandes quantidades de dados e para ver as suas capacidades verdadeiras para escalar, especialmente nas escalas maiores que oferecemos, irá querer utilizar um conjunto de dados grande que abordagens ou excede 1 TB.


## <a name="built-on-sql-server"></a>Criada com base em do SQL Server

Armazém de dados SQL é baseado no motor de base de dados relacionais do SQL Server e inclui muitas das funcionalidades que esperava a partir de um armazém de dados da empresa. Se já souber T-SQL, é fácil transferir os seus conhecimentos para armazém de dados SQL. Se são avançadas ou dar os primeiros passos, os exemplos através de documentação irão ajudá-começar. Resumindo, poderá pensar sobre a forma que recomendamos tenha construídos os elementos de idioma do armazém de dados SQL da seguinte forma:

- Armazém de dados SQL utiliza sintaxe T SQL para muitas operações. Também suporta um conjunto abrangente de tradicionais construções SQL, como procedimentos armazenados, as funções definidas pelo utilizador, a partições de tabela, índices e agrupamentos.

- Armazém de dados SQL também contém um número de funcionalidades mais recentes do SQL Server, incluindo: agrupadas **columnstore** índices, PolyBase integração e dados de auditoria (concluído com avaliação da ameaça).

- Elementos da linguagem determinadas T-SQL que sejam menos comum das cargas de trabalho de armazenamento de dados ou que são mais recentes para o SQL Server, poderão não estar disponíveis neste momento. Para mais informações, consulte a [documentação de migração][].

Com a funcionalidade e Transact-SQL que sejam comuns entre do SQL Server, armazém de dados SQL, base de dados SQL e o sistema de plataforma de análise, podem desenvolver uma solução que se adeque às suas necessidades de dados. Pode decidir onde pretende manter os seus dados, com base no desempenho, segurança e requisitos de escala e, em seguida, transferir dados conforme necessário entre diferentes sistemas.

## <a name="data-protection"></a>Proteção de dados

Armazém de dados SQL armazena todos os dados no armazenamento localmente redundante Azure Premium. Várias cópias síncronas os dados são mantidas no Centro de dados local para garantir a proteção de dados transparente em caso de falhas localizadas. Além disso, armazém de dados SQL automaticamente cópias as bases de dados ativos (retomadas) a intervalos regulares utilização de instantâneos de armazenamento do Azure. Para saber mais sobre como fazer cópia de segurança e restaurar funciona, consulte o artigo [Descrição geral de cópia de segurança e restauro][].

## <a name="integrated-with-microsoft-tools"></a>Integrado com ferramentas do Microsoft

Armazém de dados SQL também integra-se muitas das ferramentas que os utilizadores podem estar familiarizados com o SQL Server. Estes incluem:

**Ferramentas tradicional do SQL Server:** Armazém de dados SQL está totalmente integrado com o SQL Server Analysis Services, os serviços de integração e Reporting Services.

**Ferramentas baseado na nuvem:** Armazém de dados SQL pode ser utilizada juntamente com um número de novas ferramentas no Azure, incluindo fábrica de dados, a análise da cadeia, máquina aprendizagem e do Power BI. Para uma lista completa de mais, consulte o artigo [Descrição geral das ferramentas integrada][].

**Ferramentas de terceiros:** Um grande número de fornecedores de ferramenta de terceiros tem certificação integração das suas ferramentas com armazém de dados do SQL. Para obter uma lista completa, consulte o artigo [parceiros de solução armazém de dados SQL][].

## <a name="hybrid-data-sources-scenarios"></a>Cenários de origens de dados híbridos

Utilizar armazém de dados SQL com PolyBase permite aos utilizadores sem precedentes capacidade para mover dados entre os respetivos ecossistema, desbloquear a capacidade de configuração avançadas híbrido cenários com não relacionais e origens de dados no local.

Polybase permite-lhe tirar partido de dados de origens diferentes, utilizando os comandos habituais T-SQL. Polybase permite-lhe para consultar os dados que não sejam relacionais contidos em armazenamento de Blobs do Azure, tal como se fosse uma tabela normal. Utilize Polybase para consultar os dados que não sejam relacional ou para importar dados que não sejam relacionais para armazém de dados SQL.

- PolyBase utiliza tabelas externas para aceder aos dados que não sejam relacionais. As definições de tabela estão armazenadas no armazém de dados do SQL e pode aceder aos mesmos, utilizando SQL e ferramentas como teria acesso a dados relacionais normais.

- Polybase é desconhecido na sua integração. -Expõe as mesmas funcionalidades da todas as fontes que suporta. Os dados de leitura Polybase podem ser numa variedade de formatos, incluindo ficheiros delimitados ou ficheiros ORC.

- PolyBase pode ser utilizada para aceder ao armazenamento de BLOBs também está a ser utilizado como armazenamento para um cluster de HDInsight. Isto dá-lhe acesso aos mesmos dados com ferramentas não relacionais e.

## <a name="sla"></a>SLA

Armazém de dados SQL oferece um produto nível nível contrato de serviço (SLA) como parte do Microsoft Online Services SLA. Para obter mais informações, visite [SLA para armazém de dados SQL][]. Para obter informações sobre todos os outros produtos SLA pode visitar o Azure [Acordos do nível de serviço] de página ou transferi-los na página de [Licenciamento em Volume][] . 

## <a name="next-steps"></a>Próximos passos

Agora que já conhece um pouco sobre armazém de dados SQL, saiba como rapidamente [criar um armazém de dados SQL][] e [carregar os dados de exemplo][]. Se estiver familiarizado com o Azure, que poderão ser úteis [Glossário do Azure][] à medida que encontrar terminologia nova. Em alternativa, veja algumas destes outros recursos do SQL dados armazém.  

- [Casos de sucesso do cliente]
- [Blogues]
- [Pedidos de funcionalidade]
- [Vídeos]
- [Blogues da equipa Consultivo de cliente]
- [Criar bilhetes de suporte]
- [Fórum MSDN]
- [Fórum de área de excesso de pilha]
- [Twitter]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar bilhetes de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[carregar os dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[criar um armazém de dados SQL]: ./sql-data-warehouse-get-started-provision.md
[Documentação de migração]: ./sql-data-warehouse-overview-migrate.md
[Parceiros de solução armazém de dados SQL]: ./sql-data-warehouse-partner-business-intelligence.md
[Descrição geral das ferramentas integrada]: ./sql-data-warehouse-overview-integrate.md
[Descrição geral de cópia de segurança e restauro]: ./sql-data-warehouse-restore-database-overview.md
[Glossário do Azure]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Casos de sucesso do cliente]: https://customers.microsoft.com/search?sq=&ff=story_products_services%26%3EAzure%2FAzure%2FAzure%20SQL%20Data%20Warehouse%26%26story_product_families%26%3EAzure%2FAzure%26%26story_product_categories%26%3EAzure&p=0
[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da equipa Consultivo de cliente]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Pedidos de funcionalidade]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum de área de excesso de pilha]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA para armazém de dados SQL]: https://azure.microsoft.com/en-us/support/legal/sla/sql-data-warehouse/v1_0/
[Licenciamento em volume]: http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Acordos do nível serviço]: https://azure.microsoft.com/en-us/support/legal/sla/
