<properties
   pageTitle="Azure SQL da base de dados Web Business Edition-sol FAQ sobre e | Microsoft Azure"
   description="Descubra quando as bases de dados Web de SQL Azure e empresas irão foi removidas e saiba mais sobre as funcionalidades e das novas camadas de serviço."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>FAQ-sol Web e Business Edition

Azure bases de dados Web de SQL e empresas são agora foi removidas. As camadas Basic, padrão, Premium e elásticos substituem as retiring bases de dados Web e empresas.

Para ajudá-lo com a actualização de bases de dados Web e empresas, o serviço de base de dados SQL recomenda um adequado camada e desempenho do nível de serviço (preços camada) para cada base de dados com base na sua carga de trabalho histórica.

**Para obter preços recomendações de camadas:**

- [Atualização para o V12 de base de dados do SQL Azure no portal](sql-database-upgrade-server-portal.md)
- [Atualização para o SQL V12 de base de dados através do PowerShell](sql-database-upgrade-server-powershell.md)
- [Alterar a camada preços de uma base de dados Web ou para empresas](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>Por que motivo é que o portal do Azure Mostrar minhas Web e empresas edition bases de dados tal como foi removida?

Porque Web e empresas edition as bases de dados não estarão disponíveis após Setembro de 2015, o portal de etiquetas de Web e empresas bases de dados que foi removida. A etiqueta retirada fornece também um lembrete que quaisquer bases de dados Web e empresas devem ser atualizadas padrão, Basic ou Premium. Para obter informações detalhadas sobre como atualizar bases de dados Web ou empresas existentes para as novas camadas de serviço, consulte o artigo [Atualizar para o V12 de base de dados do SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>Qual nova camada de serviços é a melhor escolha para atualizar o meu Web ou empresas base de dados existente para?

Selecionar um novo serviço camada e desempenho nível adequado para a base de dados existente do Web ou empresas depende os requisitos específicos de desempenho e para a sua aplicação.

Utilize as recomendações de camada comparar descritas acima, ou para obter informações detalhadas para ajudá-lo a selecionar uma nova camada de serviço adequado, consulte o artigo [Atualizar para V12 de base de dados do SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>Por que motivo é Microsoft introdução ao novo camadas de serviços?

Com base nos comentários, base de dados do SQL Azure é introdução ao novo camadas de serviço para ajudar os clientes mais facilmente suporte cargas de trabalho de base de dados relacional. As novas camadas foram concebidas para disponibilizar previsível desempenho ao longo de um espectro de sete níveis para light-espessura a pedidos de aplicação transaccional carregado. Para além disso, as novas camadas oferecem uma variedade de funcionalidades de continuidade business, um SLA mais forte do tempo de utilização, grande tamanho da base de dados para o menos dinheiro e uma experiência de faturação melhorada.

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>Onde posso saber mais sobre as novas camadas de serviço?

Para obter informações detalhadas sobre o novo camadas de serviços e o modelo de desempenho, consulte o artigo [camadas de serviços](sql-database-service-tiers.md). Para obter detalhadas estimados para as novas camadas de serviço, consulte o artigo [preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>Que funcionalidades ou funcionalidade não estará disponível no Basic, padrão e Premium?

A funcionalidade de profissionais vai ser foi removida com as edições Web e empresas. Os clientes que precisam de escala-out suas bases de dados estão convidos em vez disso, utilize ou migrar para [Ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md) para a [Base de dados do SQL Azure](sql-database-elastic-scale-get-started.md), qual simplifica a criar e gerir uma aplicação que utiliza sharding. Uma biblioteca de cliente .NET permite que as aplicações definir a forma como os dados são mapeados para shards e rotas OLTP os pedidos para bases de dados adequadas. Para suportar operações de gestão de reconfigurar como os dados são distribuídos entre shards, um modelo de serviço de nuvem Azure incluída que pode alojar na sua própria subscrição Azure. Para além de [Ferramentas da base de dados flexível](sql-database-elastic-scale-get-started.md), Microsoft irão continuar a criar e publicar [orientações de padrões e práticas sharding personalizada](https://msdn.microsoft.com/library/azure/dn764977.aspx) com base em learnings a partir das atribuições de cliente abrangente. Novos clientes que precisam de escala de saída funcionalidade deverão dar saída de [Ferramentas da base de dados flexível](sql-database-elastic-scale-get-started.md) e/ou contacte o Microsoft Support avaliar as suas opções.

Microsoft também está a mudar a experiência de cópia da base de dados com bases de dados Premium. CRIE anteriormente quota da base de dados de premium foi limitado, base de dados... Uma cópia em T-SQL criado um suspensa Premium base de dados sem recursos reservados, que foi cobrado à mesma velocidade como uma base de dados de negócio. Como premium quota está agora mais livre disponível, suspensa Premium já não é suportada. Cópias de base de dados agora serão criadas com o mesmo de edição e o nível de desempenho como a origem e irão ser faturadas em conformidade. Os clientes podem optar por alterar copiadas bases de dados para um nível de desempenho ou camada de serviços diferente para reduzir o seu custo se pretender. Bases de dados existentes suspensa Premium serão convertido em edição de negócio como parte nesta versão. É antecipada que a introdução de [Ponto-In-Time restaurar](sql-database-recovery-using-backups.md#point-in-time-restore) irá reduzir a necessidade de fazer cópias de segurança das bases de dados.

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>Como é que Basic, padrão e Premium melhorar minha faturação experiência?

Básicas, Standard e bases de dados SQL Azure de Premium são faturadas por hora e tiver a capacidade de dimensionar cada base de dados para cima ou para baixo. São faturada com uma taxa fixa com base no mais alto camada e desempenho do nível de serviço que escolher para cada hora. Para além disso, os níveis de desempenho (exemplo: Basic, S1 e P2) são divididos na fatura para que seja mais fácil de ver o número de dias/horas de base de dados suportados num mês único para cada nível de desempenho. Bases de dados Web e empresas continuam a ser faturada utilizando as unidades de base de dados com base no tamanho da base de dados. Visite a [página de preços de base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) para saber mais sobre os preços e diferenças entre as novas camadas de serviço.


## <a name="see-also"></a>Consulte também

[Base de dados Azure SQL](https://azure.microsoft.com/documentation/services/sql-database/)

[Web e preços de empresas](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Camadas de serviços](sql-database-service-tiers.md)

[Atualizar para o V12 de base de dados do Azure SQL](sql-database-upgrade-server-portal.md)
