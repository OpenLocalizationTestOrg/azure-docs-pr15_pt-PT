<properties
    pageTitle="O que é a base de dados SQL? Introdução à base de dados SQL | Microsoft Azure"
    description="Obter uma introdução à base de dados SQL: detalhes técnicos e as funcionalidades da Microsoft relacionais da base de dados o sistema de gestão (RDBMS) na nuvem."
    keywords="introdução a sql, aqui para sql, o que é a base de dados sql"
    services="sql-database"
    documentationCenter=""
    authors="shontnew"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="shkurhek"/>

# <a name="what-is-sql-database-introduction-to-sql-database"></a>O que é a base de dados SQL? Introdução à base de dados SQL

Base de dados SQL é um serviço de base de dados relacionais na nuvem com base em motor de Microsoft SQL Server líder de mercado, com capacidades críticos. Base de dados SQL proporciona um desempenho previsível, escalabilidade com sem tempo de inatividade, continuidade do negócio e proteção de dados — tudo com administração perto zero. Pode concentrar-se no desenvolvimento de aplicação rápida e acelerar o seu tempo a mercado, em vez de gerir máquinas virtuais e infraestrutura. Uma vez que baseia-se o motor de [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) , base de dados SQL suporta existente do SQL Server ferramentas, bibliotecas e APIs, que facilita mover e expandir na nuvem.

Este artigo é uma introdução à conceitos de core de base de dados SQL e funcionalidades relacionadas com o desempenho, escalabilidade e capacidade de gestão, com ligações para explorar detalhes. Se estiver pronto para iniciar no, pode [criar a primeira base de dados do SQL](sql-database-get-started.md) ou [criar um agrupamento de base de dados flexível](sql-database-elastic-pool-create-portal.md) em minutos. Se pretender que um vôo picado mais aprofundado, veja este vídeo de 30 minutos.

> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]

## <a name="adjust-performance-and-scale-without-downtime"></a>Ajustar o desempenho e a escala sem tempo de inatividade

Bases de dados SQL está disponível no Basic, padrão e Premium *camadas de serviços*. Cada camada de serviço oferece [diferentes níveis de desempenho e capacidades](sql-database-service-tiers.md) para suportar lightweight para pesado da base de dados das cargas de trabalho. Pode criar a aplicação primeira numa base de dados pequenas para alguns Beira Litoral por mês, em seguida, [alterar a camada de serviço](sql-database-scale-up.md) manualmente ou através de programação em qualquer altura tal como a sua aplicação acede em todo o mundo, sem tempo de inatividade a sua aplicação ou os seus clientes.

Para muitos e apps de empresas, ser capaz de criar bases de dados e fazer a marcação desempenho única base de dados para cima ou para baixo a pedido é suficiente, sobretudo se padrões de utilização são relativamente previsíveis. Mas, se tiver padrões de utilização inesperados,-pode torná-la disco rígido gerir custos e o seu modelo de negócio.

[Flexível agrupamentos](sql-database-elastic-pool.md) de base de dados SQL resolver este problema. O conceito é simple. Alocar desempenho para um conjunto de dados e pagar o desempenho do conjunto de coletivos em vez de desempenho única base de dados. Não precisa de marcar o desempenho da base de dados para cima ou para baixo. As bases de dados no conjunto, denominado *flexível bases de dados*, dimensionar automaticamente para cima e para baixo até encontro pedido. Bases de dados flexível consumam mas não excedam os limites do conjunto de, para que o custo permanece previsível mesmo se não a utilização de base de dados. O que acontece mais, pode [Adicionar e remover as bases de dados para o conjunto de dados](sql-database-elastic-pool-manage-portal.md), dimensionamento a aplicação a partir de alguns das bases de dados milhares, tudo dentro de um orçamento que pode controlar o. Para saber mais sobre os padrões de estrutura para aplicações SaaS utilizando flexível conjuntos de dados, consulte o artigo [Padrões de Design para inquilino com várias SaaS aplicações com base de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Ambas as formas que aceda — única ou flexível — não estiver bloqueada. Pode misturar a única bases de dados com agrupamentos de base de dados flexível e alterar as camadas de serviço das bases de dados simples e conjuntos de dados para criar estruturas de forma inovadoras. Além disso, com o power e alcance do Azure, pode misturar e CORRESP serviços Azure com base de dados do SQL para corresponder às suas necessidades de estrutura da aplicação moderna exclusiva, unidade eficiência de custos e de recursos e desbloquear novas oportunidades de negócio.

Mas como pode comparar o desempenho relativo de bases de dados e agrupamentos de base de dados? Como sabe o botão direito do rato clique-parar quando marcar cima e para baixo? A resposta é a unidade da transação da base de dados (DTU) para bases de dados únicos e o DTU flexível (eDTU) para bases de dados flexível e agrupamentos de base de dados. Consulte o artigo [Opções de base de dados SQL e o desempenho: compreender o que está disponível em cada camada de serviço](sql-database-service-tiers.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Manter as suas aplicações e negócio em funcionamento

Indústria líder 99,99% disponibilidade nível contrato de serviço do Azure [(SLA)](http://azure.microsoft.com/support/legal/sla/), recorrendo a uma rede global de geridas pelo Microsoft centros de dados ajuda a manter a sua aplicação a executar o 24/7. Cada base de dados do SQL tira partido de proteção de dados incorporados, tolerância a falhas e proteção de dados caso contrário, teria de estruturar, comprar, criar e gerir. Mesmo assim, dependendo de pedidos da sua empresa, podem exigir camadas adicionais de proteção para garantir que a sua aplicação e a sua empresa podem recuperar rapidamente trabalho uma falhas, um erro ou outra coisa. Base de dados SQL, cada camada de serviço oferece um menu diferente de funcionalidades que pode utilizar para ajudar o e em execução e manter-se dessa forma. Pode utilizar em qualquer altura restaurar para devolver uma base de dados a um estado anterior, até ao 35 dias. Além disso, se o Centro de dados que aloja as bases de dados experiências uma falha, pode activação pós-falha para réplicas de base de dados numa região diferente. Ou pode utilizar réplicas para atualiza os ou de mudança para regiões diferentes.

![Geo-replicação de bases de dados SQL](./media/sql-database-technical-overview/azure_sqldb_map.png)


Consulte o artigo [Continuidade do negócio](sql-database-business-continuity.md) para obter detalhes sobre as funcionalidades de continuidade do negócio diferentes disponíveis para camadas de serviços diferente.

## <a name="secure-your-data"></a>Proteger os seus dados
SQL Server tem uma tradição de segurança dos dados sólida que a base de dados SQL respeite com funcionalidades que limitar o acesso, proteger dados e ajudá-lo a monitorizar a atividade. Consulte a [proteger a sua base de dados do SQL](sql-database-security.md) para uma rápida execução das opções de segurança que tiver na base de dados SQL. Consulte o [Centro de segurança do motor de base de dados do SQL Server e base de dados SQL](https://msdn.microsoft.com/library/bb510589) para uma vista mais abrangente de funcionalidades de segurança. E visite o [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/security/) para obter informações sobre segurança da plataforma do Azure.

## <a name="next-steps"></a>Próximos passos
Agora que já ler uma introdução à base de dados SQL e respondeu à pergunta "Qual é a base de dados SQL?", está pronto para:

- Consulte a [página de preços](https://azure.microsoft.com/pricing/details/sql-database/) para única base de dados e comparações de custo de base de dados flexível e calculadoras.
- Saiba mais sobre [flexível conjuntos de dados](sql-database-elastic-pool.md).
- Introdução ao [criar a sua primeira base de dados](sql-database-get-started.md).
- [Ligar e de consulta com SSMS](sql-database-connect-query-ssms.md)
- Construir a sua aplicação primeira na c#, Java, Node.js, PHP, Python ou Rubi: [bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md)
- Consulte o artigo um índice de todos os títulos e as descrições de [todos os tópicos do serviço do sql Azure - base de dados](sql-database-index-all-articles.md).
