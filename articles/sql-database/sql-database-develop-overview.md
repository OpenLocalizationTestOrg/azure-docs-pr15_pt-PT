<properties
    pageTitle="Descrição geral de desenvolver de base de dados SQL | Microsoft Azure"
    description="Saiba mais sobre bibliotecas de conectividade disponíveis e as melhores práticas para ligar à base de dados SQL de aplicações."
    services="sql-database"
    documentationCenter=""
    authors="annemill"
    manager="jhubbard"
    editor="genemi"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="annemill"/>

# <a name="sql-database-development-overview"></a>Descrição geral do desenvolvimento de base de dados SQL
Este artigo explica as considerações básicas que um programador deve ter em mente quando escrever o código para ligar à base de dados do SQL Azure.

## <a name="language-and-platform"></a>Idioma e a plataforma
Existem exemplos de código disponíveis para vários idiomas e plataformas de programação. Pode encontrar ligações para os exemplos de código em: 

* Mais informações: [as bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md)

## <a name="resource-limitations"></a>Limitações de recursos
Base de dados SQL Azure gere os recursos disponíveis para uma base de dados utilizando dois mecanismos diferentes: imposição de limites e governação de recursos.

* Mais informações: [limites de recursos base de dados SQL Azure](sql-database-resource-limits.md)

## <a name="security"></a>Segurança
Base de dados SQL Azure fornece recursos para limitar o acesso, proteger dados e monitorizar atividades numa base de dados do SQL.

* Mais informações: [proteger a sua base de dados SQL](sql-database-security.md)

## <a name="authentication"></a>Autenticação
* Base de dados SQL Azure suporta os utilizadores de autenticação do SQL Server e inícios de sessão, bem como os utilizadores de [autenticação do Azure Active Directory](sql-database-aad-authentication.md) e inícios de sessão.
* Precisa de especificar uma base de dados específico, em vez de a predefinir para a base de dados *principal* .
* Não é possível utilizar a instrução Transact-SQL **utilizar myDatabaseName;** na base de dados SQL para mudar para outra base de dados.
* Obter mais informações: [segurança da base de dados SQL: Gerir a segurança da base de dados do access e inicie sessão](sql-database-manage-logins.md)

## <a name="resiliency"></a>RDP
Quando ocorre um erro breves ao ligar à base de dados SQL, o seu código deve repetir a chamada.  Recomendamos que volte a tentar lógica de duplicar de utilização de lógica, para que não-sobrecarregar a base de dados do SQL com vários clientes a repetir em simultâneo.

* O código de amostras: para obter exemplos de código que ilustram a lógica de repetição, consulte exemplos para o idioma da sua escolha na: [bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md)
* Obter mais informações: [mensagens de erro para os programas de cliente da base de dados SQL](sql-database-develop-error-messages.md)

## <a name="managing-connections"></a>Gerir as ligações
* Na sua lógica de ligação de cliente, substitui o tempo limite predefinido para ser 30 segundos.  A predefinição de 15 segundos é demasiado pequeno para ligações que dependem da internet.
* Se estiver a utilizar um [conjunto de ligações](http://msdn.microsoft.com/library/8xx3tyca.aspx), certifique-se de que feche a ligação instantâneas o programa é não utilizar ativamente-lo e não está a preparar para reutilizá-la.

## <a name="network-considerations"></a>Considerações sobre a rede
* No computador que aloja o seu programa de cliente, certifique-se de que a firewall permite o envio de TCP de comunicação, na porta 1433.  Obter mais informações: [Configurar uma firewall de base de dados do SQL Azure](sql-database-configure-firewall-settings.md)
* Se o programa cliente liga-se ao V12 de base de dados do SQL enquanto o cliente é executada numa máquina virtual Azure (VM), tem de abrir determinados intervalos de porta a VM. Obter mais informações: [as portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
* Ligações de cliente V12 de base de dados do SQL Azure, por vezes, ignoram o proxy e interagem diretamente com a base de dados. Portas que não seja 1433 tornam-se importantes. Obter mais informações: [as portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

## <a name="data-sharding-with-elastic-scale"></a>Sharding de dados com escala flexível
Escala flexível simplifica o processo de dimensionamento saída (e em). 

* [Padrões de design para aplicações do inquilino com várias SaaS com base de dados Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
* [Começar a trabalhar com a pré-visualização da escala flexível de base de dados do Azure SQL](sql-database-elastic-scale-get-started.md)

## <a name="next-steps"></a>Próximos passos

Explore todas as [capacidades da base de dados SQL](https://azure.microsoft.com/services/sql-database/).
