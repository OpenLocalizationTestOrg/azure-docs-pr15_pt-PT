<properties
    pageTitle="Resolver problemas comuns de ligação à base de dados do SQL Azure"
    description="Passos para identificar e resolver erros comuns de ligação para a base de dados do SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Resolução de problemas de ligação à base de dados do SQL Azure

Quando a ligação à base de dados do Azure SQL falha, recebe [mensagens de erro](sql-database-develop-error-messages.md). Este artigo é um tópico centralizado que o ajuda a resolução de problemas de conectividade de base de dados do Azure SQL. -Apresenta [causas comuns](#cause) dos problemas de ligação, recomenda [uma ferramenta de resolução de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que ajuda a identidade o problema e fornece os passos de resolução de problemas para resolver [erros de breves](#troubleshoot-transient-errors) e [persistentes ou não transitório erros](#troubleshoot-the-persistent-errors). Por fim, apresenta uma lista [todos os artigos para problemas de conectividade de base de dados do SQL Azure](#all-topics-for-azure-sql-database-connection-problems).

Se encontrar problemas de ligação, experimente os passos de resolução de problemas que são descritos neste artigo.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa

Problemas de ligação podem ser causados por um dos seguintes procedimentos:

- Falha ao aplicar melhores práticas e estrutura diretrizes durante o processo de estrutura da aplicação.  Aceder a [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md) para começar a utilizar.
- Reconfiguração de base de dados SQL Azure
- Definições da firewall
- Tempo limite da ligação
- Informações de início de sessão incorretos
- Limite máximo de instalações atingido no alguns recursos de base de dados do Azure SQL

Em geral, os problemas de ligação à base de dados do SQL Azure podem ser classificados da seguinte forma:

- [Erros breves (curto ou intermitentes)](#troubleshoot-transient-errors)
- [Persistentes ou não transitório erros (erros que regularmente como periódico)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Experimente a resolução de problemas para problemas de conectividade de base de dados do Azure SQL

Se se deparar com um erro de ligação específicas, experimente [Esta ferramenta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que irá ajudá-lo rapidamente identidade e resolver o problema.

## <a name="troubleshoot-transient-errors"></a>Resolver problemas de erros breves
Se a sua aplicação está com problemas breves, reveja os tópicos seguintes para obter sugestões sobre como resolver problemas e reduzir a frequência destes erros:

- [Resolução de problemas da base de dados &lt;x&gt; no servidor &lt;y&gt; não estiver disponível (erro: 40613)](sql-database-troubleshoot-connection.md)
- [Resolução de problemas, diagnosticar e evitar erros de ligação de SQL e erros breves para base de dados SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Resolução de problemas persistentes (não transitório erros)

Se a aplicação de forma persistente falhar ligar à base de dados do SQL Azure, normalmente indica um problema com um dos seguintes procedimentos:

- Configuração de firewall. A firewall do Azure SQL base de dados ou lado do cliente está a bloquear ligações a base de dados do SQL Azure.
- Reconfiguração do lado do cliente de rede: por exemplo, um novo endereço IP ou um servidor proxy.
- Erro de utilizador: por exemplo, escrito incorretamente parâmetros de ligação, tais como o nome do servidor na cadeia de ligação.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passos para resolver problemas de conectividade persistente

1.  Configure [regras de firewall](sql-database-configure-firewall-settings.md) para permitir que o cliente endereço IP.
2.  Em todas as firewalls entre o cliente e na Internet, certifique-se de que a porta 1433 é aberta para que as ligações de saída. Reveja a [Configurar a Firewall do Windows para permitir o acesso do SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) para sugestões adicionais.
3.  Verifique se a cadeia de ligação e outras definições de ligação. Consulte a secção de cadeia de ligação no [tópico de problemas de conectividade](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.  Verificar estado de funcionamento de serviço no dashboard. Se achar que existe uma falha de regional, consulte o artigo [recuperar a partir de uma falha](sql-database-disaster-recovery.md) para obter os passos para recuperar para uma nova região.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Todos os tópicos para problemas de ligação de base de dados do Azure SQL

A tabela seguinte apresenta cada tópico de problema de ligação que se aplica diretamente para o serviço de base de dados do Azure SQL.


| &nbsp; | Título | Descrição |
| --: | :-- | :-- |
| 1 | [Resolução de problemas de ligação à base de dados do SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Esta é a página de destino para a resolução de problemas de conectividade de base de dados do SQL Azure. Descreve como identificar e resolver erros de breves e persistentes ou não transitório erros na base de dados do SQL Azure. |
| 2 | [Resolução de problemas, diagnosticar e evitar erros de ligação de SQL e erros breves para base de dados SQL](sql-database-connectivity-issues.md) | Saiba como resolver problemas, diagnosticar e impedir que um erro de ligação de SQL ou breves base de dados do SQL Azure. |
| 3 | [Informações gerais de processamento de falhas breves](best-practices-retry-general.md) | Fornece informações gerais para processamento de falhas breves quando ligar à base de dados do SQL Azure. |
| 4 | [Resolver problemas de conectividade com a base de dados do Microsoft Azure SQL](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | Esta ferramenta ajuda a identidade o problema de resolver erros de ligação. |
| 5 | [Resolver problemas de "base de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível neste momento. Volte a tentar mais tarde a ligação"erro](sql-database-troubleshoot-connection.md) | Descreve como identificar e resolver um erro 40613: "base de dados &lt;x&gt; no servidor &lt;y&gt; não está disponível neste momento. Volte a tentar mais tarde a ligação." |
| 6 | [Códigos de erro SQL para aplicações de cliente da base de dados SQL: erro de ligação e outros problemas de base de dados](sql-database-develop-error-messages.md) | Fornece informações sobre códigos de erro SQL para aplicações de cliente da base de dados SQL, como erros comuns de ligação de base de dados, base de dados cópia problemas e erros gerais. |
| 7 | [Orientação de desempenho de base de dados SQL Azure para bases de dados simples](sql-database-performance-guidance.md) | Fornece orientações para ajudá-lo a determinar qual camada de serviços é à direita para a sua aplicação. Também fornece recomendações para otimizar a sua aplicação para tirar o máximo partido da sua base de dados do SQL Azure. |
| 8 | [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md) | Fornece hiperligações para exemplos de código para tecnologias de vários que pode utilizar para ligar e interagir com base de dados do SQL Azure. |
| 9 | Atualizar a página de v12 de base de dados do Azure SQL ([Azure portal](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Fornece instruções para atualizar os servidores de V11 de base de dados do SQL Azure e bases de dados existentes para V12 de base de dados do SQL Azure utilizando o Azure portal ou PowerShell. |


## <a name="next-steps"></a>Próximos passos

- [Resolução de problemas de desempenho da base de dados do Azure SQL](sql-database-troubleshoot-performance.md)
- [Resolução de problemas de permissões de base de dados do Azure SQL](sql-database-troubleshoot-permissions.md)
- [Ver todos os tópicos para o serviço de base de dados do Azure SQL](sql-database-index-all-articles.md)
- [Procurar a documentação do Microsoft Azure](http://azure.microsoft.com/search/documentation/)
- [Ver as atualizações mais recentes para o serviço de base de dados do Azure SQL](http://azure.microsoft.com/updates/?service=sql-database)


## <a name="additional-resources"></a>Recursos adicionais

- [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
- [Informações gerais de processamento de falhas breves](../best-practices-retry-general.md)
- [Bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md)
- [Caminho de formação para utilizar a base de dados do SQL Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [Caminho de formação para utilizar a base de dados flexível funcionalidades e ferramentas](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 
