<properties
    pageTitle="Descrição geral de base de dados de esticar | Microsoft Azure"
    description="Saiba como base de dados esticar migra os seus dados frios transparente e segura na nuvem da Microsoft Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>Esticar descrição geral de base de dados

Base de dados esticar migra os seus dados frios transparente e segura na nuvem da Microsoft Azure.

Se apenas quiser começar a trabalhar com esticar base de dados de imediato, consulte o artigo [Introdução ao executar a base de dados ativar para esticar assistente](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>Quais são as vantagens da base de dados esticar?
Base de dados esticar fornece os seguintes benefícios:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Fornece custo\-disponibilidade eficaz para dados frias
Esticar quentes e frios dados transaccionais dinamicamente do SQL Server para Microsoft Azure com base de dados do SQL Server esticar. Ao contrário de armazenamento de dados típico de fria, os dados estão sempre online e disponível à consulta. Pode fornecer linhas cronológicas de retenção de dados mais longa sem quebrar banco para tabelas grandes, como o histórico de encomendas de cliente. Beneficiem das vantagens dos baixo custo de Azure em vez de dimensionamento dispendioso, no\-instalações armazenamento. Selecione a camada comparar e configurar as definições no Portal do Azure para manter o controlo de custos. Dimensione para cima ou para baixo, conforme necessário. Visite a página de [Preços de base de dados do SQL Server esticar](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) para obter detalhes.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Não necessita de alterações para consultas ou aplicações
Aceder a dados do SQL Server de forma totalmente integrada independentemente de se tratar de\-instalações ou for esticada na nuvem.  Definir a política que determina onde os dados são armazenados e do SQL Server trata o movimento de dados em segundo plano. A tabela inteira sempre está online e como consultáveis. E, esticar base de dados não necessita de quaisquer alterações para consultas existentes ou aplicações – a localização dos dados é completamente transparente para a aplicação.

### <a name="streamlines-on-premises-data-maintenance"></a>Simplifica no\-instalações manutenção de dados
Reduzir no\-instalações manutenção e armazenamento para os seus dados. Cópias de segurança para o seu no\-local dados mais rápido e terminada dentro da janela de manutenção. Cópias de segurança para a parte da nuvem dos seus dados são executadas automaticamente. Seu no\-local armazenamento necessidades substancialmente são reduzidas. Armazenamento Azure pode ser 80% menos dispendioso do que adicionar para ativado\-instalações SSD.

### <a name="keeps-your-data-secure-even-during-migration"></a>Mantém os seus dados seguro mesmo durante a migração
Desfrute paz de espírito à medida que esticar as aplicações mais importantes em segurança para a nuvem. Sempre encriptados SQL Server fornece encriptação para os seus dados em movimento. Segurança de nível de linha (RLS) e outras funcionalidades de segurança avançadas do SQL Server também funcionam com base de dados de Esticar para proteger os seus dados.

## <a name="what-does-stretch-database-do"></a>O que faz esticar base de dados?
Depois de activar esticar base de dados para uma instância do SQL Server, uma base de dados e pelo menos uma tabela, esticar base de dados começa silenciosamente migrar os seus dados frios para Azure.

-   Se armazenar frios dados numa tabela separada, pode migrar toda a tabela.

-   Se a tabela contiver dados quentes e frios, pode especificar uma função de filtro para selecionar as linhas que pretende migrar.

**Não tem de alterar consultas existentes e aplicações de cliente.** Continuar a ter acesso totalmente integrado aos dados locais e remotos, mesmo durante a migração de dados. Existe uma pequena quantidade de latência para consultas remotas, mas apenas encontrar esta latência quando consulta os dados frios.

**Que base de dados esticar assegura que os dados não são perdidos** se ocorrer uma falha durante a migração. Também não tem lógica de repetição para processar problemas de ligação que podem ocorrer durante a migração. Uma vista de gestão de dinâmica fornece o estado da migração.

**Pode colocar em pausa migração de dados** para resolução de problemas no servidor local ou para maximizar a largura de banda de rede disponíveis.

![Esticar descrição geral de base de dados][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>É esticar base de dados para si?
Se pode efetuar as seguintes instruções, esticar base de dados pode ajudar a cumprir os requisitos e resolver os problemas.

|Se for um decisor|Se for um DBA|
|------------------------------|-------------------|
|Tenho de ter para manter os dados transaccionais muito tempo.|O tamanho da minha tabelas está a ficar fora do controlo.|
|Por vezes tenho de ter para consultar os dados frios.|Os meus utilizadores dizem que pretendem acesso aos dados frios, mas utilizam só raramente.|
|Tenho aplicações, incluindo as aplicações mais antigas, que não a quer atualizar.|Tenho de ter para manter comprar e adicionar mais armazenamento.|
|Pretende encontrar uma forma de poupar dinheiro armazenamento.|Não consigo fazer cópia de segurança ou restaurar essas tabelas grandes dentro de SLA.|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Que tipo de bases de dados e as tabelas são ideais para esticar base de dados?
Esticar da base de dados destinos transaccionais bases de dados com grandes quantidades de dados frias, normalmente armazenados num pequeno número de tabelas. Estas tabelas podem conter mais do que um mil milhões de linhas.

Se utilizar a funcionalidade de tabela temporal do SQL Server 2016, utilizar esticar base de dados para migrar todo ou parte da tabela histórico associados a custos\-eficaz armazenamento no Azure. Para obter mais informações, consulte o artigo [Gerir retenção de dados histórico em tabelas Temporal versões do sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Utilize a classificação de base de dados do esticar, uma funcionalidade do SQL Server 2016 atualizar Advisor, para identificar as bases de dados e as tabelas para esticar base de dados. Para obter mais informações, consulte o artigo [Identificar bases de dados e as tabelas para esticar base de dados](sql-server-stretch-database-identify-databases.md). Para saber mais sobre possíveis problemas de bloqueio, consulte [limitações para esticar base de dados](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Testar unidade esticar base de dados
**Teste a unidade esticar base de dados com a base de dados de exemplo AdventureWorks.** Para obter a base de dados de exemplo AdventureWorks, transferir, pelo menos, o ficheiro de base de dados e o ficheiro de amostras e scripts de [aqui](https://www.microsoft.com/download/details.aspx?id=49502). Depois de restaurar a base de dados de exemplo para uma instância do SQL Server 2016, deszipar o ficheiro de amostras e abra o ficheiro de esticar DB amostras a partir da pasta esticar DB. Executar os scripts neste ficheiro para verificar o espaço utilizado pelos seus dados antes e depois de activar esticar da base de dados, para controlar o progresso da migração de dados e para confirmar que pode continuar a consulta de dados existentes e inserir novos dados durante e após a migração de dados.

## <a name="next-step"></a>Passo seguinte
**Identifique bases de dados e tabelas que são candidatos para esticar base de dados.** Transferir o SQL Server 2016 atualizar Advisor e executar a classificação de base de dados do Esticar para identificar bases de dados e tabelas que são candidatos para esticar base de dados. Esticar Advisor de base de dados também identifica os problemas de bloqueio. Para obter mais informações, consulte o artigo [Identificar bases de dados e as tabelas para esticar base de dados](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
