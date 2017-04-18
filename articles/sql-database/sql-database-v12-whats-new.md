<properties
    pageTitle="O que há de novo no V12 de base de dados do SQL | Microsoft Azure"
    description="Descreve por que motivo serão vantajoso para sistemas de profissionais que estiver a utilizar a base de dados do Azure SQL na nuvem ao atualizar para versão V12 agora."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>O que há de novo no V12 de base de dados SQL


Este tópico descreve as vantagens de muitos que a nova versão do V12 da base de dados do SQL Azure tem sobre versão V11.


Podemos continuar adicionar funcionalidades à V12. Por isso, aconselhamos para visite a nossa página de atualizações de serviço Web para Azure e para utilizar os filtros:


- Filtrado para o [serviço de base de dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrado para disponibilidade geral [anúncios (das versões DG)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para funcionalidades de base de dados SQL.


As informações mais recentes sobre os limites de recursos para a base de dados SQL são documentadas em:<br/>[Limites de recursos de base de dados do azure SQL](sql-database-resource-limits.md).


## <a name="increased-application-compatibility-with-sql-server"></a>Compatibilidade de um aumento de aplicações com o SQL Server


Um objetivo chave para V12 de base de dados do SQL foi para melhorar a compatibilidade com o Microsoft SQL Server 2014 e para manter a compatibilidade como são lançadas novas versões do SQL Server. Entre outras áreas V12 atinge paridade com o SQL Server na área importante de programabilidade. Por exemplo:

- [Suporte JSON incorporado](https://msdn.microsoft.com/library/dn921897.aspx)

- [Funções da janela](http://msdn.microsoft.com/library/ms189798.aspx), com [sobre](http://msdn.microsoft.com/library/ms189461.aspx)

- [Índices XML](http://msdn.microsoft.com/library/bb934097.aspx) e [selectivos índices XML](http://msdn.microsoft.com/library/jj670104.aspx)

- [Registo de alterações](http://msdn.microsoft.com/library/bb933875.aspx)

- [SELECIONE... PARA](http://msdn.microsoft.com/library/ms188029.aspx)

- [Pesquisa de texto completo](http://msdn.microsoft.com/library/ms142571.aspx)

- [ALTERAR a configuração de SCOPED de base de dados (o Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Consulte [aqui](sql-database-transact-sql-information.md) para o conjunto de funcionalidades ainda não é suportada numa base de dados do SQL pequenas.


### <a name="compatibility-level-130"></a>Nível de compatibilidade 130


> [AZURE.IMPORTANT] *Recentemente* criado bases de dados no V12 de base de dados do SQL Azure iniciar no **2016 de Junho**, tem o respetivo nível de compatibilidade iniciar em 130, que corresponde ao Microsoft SQL Server 2016 GA.
> 
> Pode utilizar `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` se preferir.
> 
> Bases de dados criadas antes de Junho de 2016 não possui o respetivo nível de compatibilidade alterado por esta alteração de predefinido. Nem é o nível de uma base de dados alterado por atualizá-lo do V11 para V12.



Para obter uma explicação sobre como pode compara as suas consultas mais importantes entre o mais recente versus anterior nível de compatibilidade, consulte:

- [Desempenho melhorado consulta com o nível de compatibilidade 130 na base de dados Azure SQL](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>Mais premium um desempenho, novos níveis de desempenho


No V12, podemos aumentado as unidades da transação da base de dados (DTUs) atribuídos a todos os níveis de desempenho Premium por 25% de cada sem custos adicionais. Ainda maiores ganhos de desempenho podem ser obtidos com novas funcionalidades, como:


- Suporte para na memória [columnstore índices](http://msdn.microsoft.com/library/gg492153.aspx).
- [Tabela de partição por linhas](http://msdn.microsoft.com/library/ms187802.aspx) com melhoramentos relacionados para [Tabela truncar](http://msdn.microsoft.com/library/ms177570.aspx).
- A disponibilidade de gestão de dinâmica vistas [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx) para o ajudar a monitorização e o desempenho.


### <a name="reliable-performance"></a>Desempenho fiável


Se o programa cliente liga-se ao V12 de base de dados do SQL enquanto o cliente é executada numa máquina virtual Azure (VM), terá de abrir os seguintes intervalos de porta na VM:

- 11000 11999
- 14000 14999


Clique [aqui](sql-database-develop-direct-route-ports-adonet-v12.md) para obter detalhes sobre as portas para V12 de base de dados do SQL. As portas são necessários ao melhorias no desempenho no V12 de base de dados do SQL.


## <a name="better-support-for-cloud-saas-vendors"></a>Melhor suporte para a nuvem SaaS fornecedores


Apenas em V12, lançadas o novo nível de desempenho padrão S3 e a pré-visualização pública de [conjuntos de dados da base de dados flexível](sql-database-elastic-pool.md). Agrupamentos de base de dados flexível é uma solução concebido para a nuvem SaaS fornecedores.  Com agrupamentos de base de dados flexível, pode:


- Partilhar DTUs entre bases de dados para reduzir os custos de grandes quantidades de bases de dados.
- Execute [tarefas de base de dados flexível](sql-database-elastic-jobs-overview.md) para gerir bases de dados, escala.


## <a name="security-enhancements"></a>Melhoramentos de segurança


Segurança é uma questão principal para qualquer pessoa que executa o seu negócio na nuvem. As funcionalidades de segurança mais recentes publicadas em V12 incluem:


- [Segurança de nível de linha](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Máscaras dinâmico de dados](sql-database-dynamic-data-masking-get-started.md)
- [Bases de dados contidas](http://msdn.microsoft.com/library/ff929188.aspx)
- [Funções da aplicação](http://msdn.microsoft.com/library/ms190998.aspx) geridos com conceder, RECUSAR, REVOGAR
- [Encriptação de dados transparente](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Ligar à base de dados SQL utilizando a autenticação do Azure Active Directory](sql-database-aad-authentication.md)
 - Base de dados SQL suporta agora a autenticação do Azure Active Directory, um mecanismo de ligar à base de dados SQL através de identidades no Azure Active Directory (Azure AD). Com a autenticação do Azure Active Directory pode gerir centralmente as identidades dos utilizadores de base de dados e outros serviços da Microsoft numa localização central.
- [Sempre encriptados](https://msdn.microsoft.com/library/mt163865.aspx) (na pré-visualização) torna encriptação transparente para aplicações e permite que os clientes encriptar dados sensíveis a maiúsculas e dentro de aplicações de cliente sem partilhar as chaves de encriptação com base de dados SQL.


## <a name="increased-business-continuity-when-recovery-is-needed"></a>Maior continuidade do negócio quando é necessária uma recuperação


V12 oferece objetivos do ponto de recuperação melhoradas (RPOs) e recuperação estimada vezes (ERTs):


| Funcionalidade de continuidade do negócio | Versão anterior | V12 |
| :-- | :-- | :-- |
| Restaurar geo | • RPO < 24 horas.<br/>• ERTER < 12 horas. | • RPO < 1 hora.<br/>• ERTER < 12 horas. |
| Replicação de Geo ativa | • RPO < 5 minutos.<br/>• ERTER < 1 hora. | • RPO < 5 segundos.<br/>• ERTER < 30 segundos. |


Para mais informações, consulte [continuidade de negócios de base de dados SQL](sql-database-business-continuity.md) .


## <a name="more-reasons-to-upgrade-now"></a>Mais motivos pelos quais atualizar agora


Existem muitos boas razões porque é que os clientes devem atualizar agora para V12 de base de dados do SQL Azure a partir do V11:


- V12 de base de dados do SQL tem uma longa lista de funcionalidades para além das funcionalidades do V11.
- Podemos continuar adicionar novas funcionalidades V12, mas não existem novas funcionalidades serão adicionadas à V11.
- A maioria das novas funcionalidades são lançadas no V12 de base de dados do SQL antes de estão a ser lançadas para o Microsoft SQL Server.


## <a name="are-you-using-v12-already"></a>Está a utilizar V12 já?


É uma forma fácil de ver se tem uma base de dados ou lógico servidor a executar numa versão anterior do serviço de base de dados SQL fazer o seguinte:


1. Aceda ao [Azure Portal](https://portal.azure.com/).
2. Clique em **Procurar**.
3. Clique em **servidores SQL**.
4. O ícone junto ao seu servidor ou base de dados indica a história:
 - ![Ícone para um servidor de v12](./media/sql-database-v12-whats-new/v12_icon.png) **servidor lógicos V12**
 - ![Ícone para o servidor de versão anterior](./media/sql-database-v12-whats-new/earlier_icon.png) **servidor lógico de versão anterior**


Outra técnica para verificar a versão é para executar a `SELECT @@version;` instrução na sua base de dados e ver os resultados semelhantes a:


- **12**.0.2000.10 &nbsp; *(versão V12)*
- **11**.0.9228.18 &nbsp; *(versão V11)*


Uma base de dados V12 pode ser alojado apenas num servidor V12 lógico. E um servidor de V12 pode alojar apenas V12 bases de dados.


Se ainda não está executar no V12, pode atualizar o seu servidor lógico, seguindo os passos em [Atualizar para o SQL V12 de base de dados no local](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Regiões de disponibilidade gerais


- 31 de Julho de 2015, todas as regiões tivessem sido promovidas das versões geral disponibilidade (DG).
- V12 foi publicado no Dezembro de 2014, mas apenas no estado da pré-visualização.

[Termos suplementares de utilização do Microsoft Azure pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
