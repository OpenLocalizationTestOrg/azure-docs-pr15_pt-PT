<properties 
   pageTitle="Base de dados do Azure SQL perguntas mais frequentes" 
   description="Perguntar respostas aos clientes de perguntas comuns sobre bases de dados na nuvem e base de dados do SQL Azure, sistema de gestão de base de dados relacional da Microsoft (RDBMS) e base de dados como um serviço na nuvem." 
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
   ms.workload="data-management" 
   ms.date="08/16/2016"
   ms.author="sashan;carlrab"/>

# <a name="sql-database-faq"></a>Base de dados do SQL perguntas mais frequentes

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Como é que a utilização de base de dados SQL aparecem na minha fatura? 
Títulos de base de dados SQL a uma taxa de comunicação previsível com base na camada de serviço + nível de desempenho para bases de dados única ou eDTUs por agrupamento de base de dados flexível. Utilização real é calculada e proporcional hora a hora, para que a sua fatura poderá mostrar frações de uma hora. Por exemplo, se existir uma base de dados para 12 horas num mês, a sua fatura mostra a utilização de 0,5 dias. Para além disso, camadas de serviços + nível de desempenho e eDTUs por agrupamento são divididos na fatura para que seja mais fácil de ver o número de dias de base de dados que utilizou para cada num único mês.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>O que acontece se única base de dados está ativa para menos de uma hora ou utiliza uma camada de serviço mais elevada para menos de uma hora?
São faturada por cada hora existe uma base de dados utilizando a camada de serviço mais alta + nível de desempenho que aplicados durante essa hora, independentemente de utilização ou se a base de dados foi ativo menos de uma hora. Por exemplo, se criar uma base de dados simples e eliminá-la mais tarde cinco minutos a sua fatura reflete uma taxa por hora de uma base de dados. 

Exemplos
    
- Se criar uma base de dados básico e, em seguida, atualize-imediatamente S1 padrão, que lhe ser cobrada à taxa padrão S1 para a primeira hora.

- Se atualizar uma base de dados do Basic para Premium às 10:00 e atualização for concluída à 1:35:00 no dia seguinte, que lhe ser cobrada à taxa Premium começando na 1:00:00 

- Se alterar uma base de dados a partir do Premium para básico às 11:00 e que é concluído no 2:15 p.m., em seguida, a base de dados é cobrada a taxa de Premium até as 3:00, após o qual-é cobrada às taxas básicas.

## <a name="how-does-elastic-database-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Como é que a utilização do conjunto de base de dados flexível aparecem na minha fatura e o que acontece quando altero eDTUs por agrupamento?
Taxas de agrupamento de base de dados flexível aparecem na sua fatura como DTUs flexível (eDTUs) em incrementos mostrados em eDTUs por agrupamento na [página de preços](https://azure.microsoft.com/pricing/details/sql-database/). Não existe nenhuma Rateado por base de dados para agrupamentos de base de dados flexível. São faturada para cada hora que existe um conjunto de dados na eDTU mais alto, independentemente de utilização ou o conjunto de estava ativo para menos de uma hora. 

Exemplos

- Se criar um agrupamento de base de dados flexível padrão com 200 eDTUs às 11:18, adicionar cinco bases de dados para o conjunto de dados, é cobrados por 200 eDTUs para a hora toda, começando às 11. através do resto do dia.
- No dia 2, às 5:05 horas, da base de dados 1 começa consumir 50 eDTUs e detém estável através do dia. Bases de dados 2 a 5 flutuam entre 0 e 80 eDTUs. Durante o dia, adicione cinco outras bases de dados consumam variados eDTUs ao longo do dia. Dia 2 é um dia inteiro faturado na 200 eDTU. 
- Num dia 3, às 5 horas. adicionar outra bases de dados 15. A utilização da base de dados aumenta ao longo do dia para o ponto onde decidir aumentar eDTUs para o conjunto de 200 a 400 às 8:05 p.m. Taxas de nível de 200 eDTU estavam em efeito até 8 pm e aumenta a 400 eDTUs para as horas de quatro restantes. 

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-database-pool-show-up-on-my-bill"></a>Como é que a utilização da Geo-replicação do Active num conjunto de dados da base de dados flexível aparecem na minha fatura?
Ao contrário das bases de dados únicos, a utilizar [Geo-a replicação do Active](sql-database-geo-replication-overview.md) com bases de dados flexível não ter um impacto de faturação direto.  Apenas são cobrados para eDTUs aprovisionada para cada um dos conjuntos de dados (conjunto primário e agrupamento secundário)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Como é que a utilização da funcionalidade auditoria afeta a minha fatura? 
Auditoria incorporada no serviço de base de dados SQL sem extra de custos e está disponível para as bases de dados Basic, padrão e Premium. No entanto, para armazenar os registos de auditoria, as utilizações de funcionalidade auditoria uma conta de armazenamento do Windows Azure e taxas para tabelas e consultas de armazenamento do Windows Azure aplicam-se baseiam o tamanho do registo de auditoria.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-database-pools"></a>Como posso encontrar o nível de camadas e o desempenho do serviço direita para bases de dados simples e agrupamentos de base de dados flexível? 
Existem algumas ferramentas disponíveis para si. 

- Para bases de dados no local, utilize a [classificação de redimensionamento DTU](http://dtucalculator.azurewebsites.net/) recomendar bases de dados e DTUs obrigatórias e avaliar a várias bases de dados para agrupamentos de base de dados flexível.
- Se uma base de dados única seria beneficiar sejam num conjunto de dados, o motor inteligente do Azure recomenda um agrupamento de base de dados flexível se vê-lo a um padrão de utilização históricas merece-lo. Consulte o artigo [Monitor e gerir um conjunto de base de dados flexível com o portal do Azure](sql-database-elastic-pool-manage-portal.md). Para obter detalhes sobre como efetuar os cálculos matemáticos si, consulte o artigo [preço e desempenho considerações para um agrupamento de base de dados flexível](sql-database-elastic-pool-guidance.md)
- Para ver se precisa de marcar uma base de dados simples para cima ou para baixo, consulte o artigo [Desempenho seta de quatro pontas única bases de dados](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>Com que frequência posso alterar o nível de camada ou desempenho de serviço de uma única base de dados? 
Com V12 bases de dados, pode alterar a camada de serviço (entre Basic, o padrão e Premium) ou o nível de desempenho dentro de uma camada de serviço (por exemplo, S1 para S2) vezes que pretende. Para bases de dados de versão anteriores, pode alterar o nível de serviço camada ou desempenho um total de quatro vezes num período de 24 horas.

##<a name="how-often-can-i-adjust-the-edtus-per-pool"></a>Com que frequência pode ajustar os eDTUs por agrupamento? 
Vezes que pretender.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-database-pool"></a>Quanto tempo demora para alterar o nível de camada ou o desempenho do serviço de uma base de dados única ou mover uma base de dados e terminar um agrupamento de base de dados flexível? 
Alterar a camada de serviço de uma base de dados e mover e terminar um agrupamento de requerem a base de dados sejam copiados a plataforma como uma operação de fundo. Alterar a camada de serviço pode tomar a partir de alguns minutos para várias horas, dependendo do tamanho das bases de dados. Em ambos os casos, as bases de dados permanecem online e disponível durante a mover. Para obter detalhes sobre como alterar as bases de dados única, consulte o artigo [alterar a camada de serviço de uma base de dados](sql-database-scale-up.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quando deve utilizar uma base de dados única vs. flexível bases de dados? 
Em geral, agrupamentos de base de dados flexível foram concebidos para normal [padrão de aplicação do software-como-a-service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md), onde não existe uma base de dados por inquilino ou cliente. Comprar bases de dados individuais e overprovisioning para cumprir a variável e pedido para cada base de dados de pico são frequentemente não custo eficiente. Com os conjuntos de dados, gerir o desempenho do conjunto de coletivos e as bases de dados para cima e para baixo Dimensionar automaticamente. 

Motor inteligente do Azure recomenda um agrupamento para bases de dados quando um padrão de utilização merece-lo. Para obter detalhes, consulte o artigo [da base de dados do SQL preços recomendações de camadas](sql-database-service-tier-advisor.md). Para uma orientação detalhada sobre como escolher entre bases de dados simples e flexível, consulte o artigo [preço e desempenho considerações para agrupamentos de base de dados flexível](sql-database-elastic-pool-guidance.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>O que significa ter até 200% do seu armazenamento de base de dados aprovisionada máximo para o armazenamento de cópia de segurança? 
Armazenamento de cópia de segurança é o armazenamento associado com as cópias de segurança automatizado da base de dados que são utilizadas para [ponto-em-tempo-restauro](sql-database-recovery-using-backups.md#-point-in-time-restore) e [Geo restaurar](sql-database-recovery-using-backups.md#geo-restore). Base de dados do Microsoft Azure SQL fornece até 200% do seu armazenamento de base de dados aprovisionada máximo de armazenamento de cópia de segurança sem custos adicionais. Por exemplo, se tiver uma instância de DB padrão com um tamanho de DB aprovisionada de 250 GB, são-lhe fornecidos com 500 GB de armazenamento de cópia de segurança sem custos acrescidos. Se a base de dados excede o armazenamento de cópia de segurança fornecido, pode escolher reduzir o período de retenção ao contactar o suporte do Azure ou pagar o armazenamento de cópia de segurança extra faturado à taxa padrão de acesso de leitura geograficamente armazenamento redundante (GRS RT). Para mais informações sobre faturação GRS RT, consulte os detalhes de preços de armazenamento.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Posso estou a mudar do Web/empresas para o novo camadas de serviço, o que preciso de saber?
Azure bases de dados Web de SQL e empresas são agora foi removidas. As camadas Basic, padrão, Premium e elásticos substituem as retiring bases de dados Web e empresas. Vamos tenha FAQ adicionais que deverá ajudá-lo durante este período de transição. [FAQ-sol Web e Business Edition](sql-database-web-business-sunset-faq.md)

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>O que é um atraso de replicação esperado quando replicação geo uma base de dados entre duas regiões dentro a geografia Azure mesmo?  
Vamos estiver atualmente a suportar um RPO de cinco segundos e o tempo de desfasamento replicação foi inferior a que, quando o secundário geo está alojado no Azure recomendado par região e na mesma camada de serviço.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>O que é um atraso de replicação esperado quando é criado geo secundário na mesma região como a base de dados principal?  
Com base em dados empíricas, não existe demasiado diferença entre região dentro e tempo de desfasamento dependências entre região replicação quando o Azure recomendado região emparelhado é utilizado. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Se existir uma falha de rede entre duas regiões, como a lógica de repetição funciona quando estiver definida Geo replicação?  
Se existir um desligar, podemos repetir todas as 10 segundos restabelecer ligações.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>O que pode fazer garante que uma alteração crítica da base de dados principal é replicada?
Geo-secundária é uma réplica assíncrona e podemos não tente mantê-lo na sincronização completa com a página principal. Mas fornecemos um método para forçar a sincronização para garantir a replicação de alterações críticas (por exemplo, as atualizações de palavra-passe). Sincronização forçada impactos desempenho porque bloqueia tópico de chamada até todas as operações consolidadas são replicadas. Para obter detalhes, consulte o artigo [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Que ferramentas estão disponíveis para monitorizar a replicação desfasamento entre a base de dados principal e geo secundário?
Vamos expor o tempo de desfasamento replicação em tempo real entre a base de dados principal e geo-secundária através das DMV. Para obter detalhes, consulte o artigo [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).
