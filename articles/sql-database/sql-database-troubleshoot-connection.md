<properties
    pageTitle="Base de dados no servidor não está disponível neste momento, ligar-se a base de dados do SQL | Microsoft Azure"
    description="Resolução de problemas da base de dados no servidor não está atualmente disponível erro quando uma aplicação liga à base de dados SQL."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="base de dados no servidor não está disponível neste momento, ligar à base de dados sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Erro "Base de dados no servidor não está disponível neste momento" ao ligar a base de dados sql
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Quando uma aplicação se liga a uma base de dados do Azure SQL, recebe a seguinte mensagem de erro:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Esta mensagem de erro é normalmente breves (de curta duração).

Este erro ocorre quando a base de dados Azure está a ser movido (ou reconfigurar) e a sua aplicação perde a ligação à base de dados SQL. Eventos de reconfiguração de base de dados do SQL ocorre devido a um evento planeado (por exemplo, uma atualização de software) ou um evento não planeado (por exemplo, uma falha de sistema do processo, ou balanceamento de carga). A maioria dos eventos reconfiguração são geralmente curto e deve ser concluída no máximo em menos de 60 segundos. No entanto, estes eventos, ocasionalmente, podem demorar mais tempo para concluir, tal como quando uma transação grande faz com que uma recuperação de execução longa.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Passos para resolver problemas de conectividade breves
1.  Verifique o [Dashboard de serviço do Microsoft Azure](https://azure.microsoft.com/status) para qualquer falhas conhecidas que ocorreram durante o período de tempo durante o qual foram relatados os erros pela aplicação.
2. As aplicações que ligar a um serviço na nuvem, tais como base de dados do SQL Azure devo esperar eventos reconfiguração periódicos e implementar a lógica para processar estes erros em vez de para revestimentos estas como erros de aplicação para utilizadores de repetição. Reveja a secção de [erros breves](sql-database-connectivity-issues.md) e as melhores práticas e diretrizes na [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md) para obter mais informações gerais de e volte a tentar estratégias de estrutura. Em seguida, consulte o artigo exemplos de código em [Bibliotecas de ligações para a base de dados SQL e o SQL Server](sql-database-libraries.md) para detalhes.
3.  Tal como uma base de dados abordagens seus limites de recursos, pode parece um problema de conectividade breves. Consulte o artigo [resolução de problemas de desempenho](sql-database-troubleshoot-performance.md).
4.  Se continuarem a problemas de conectividade, ou se a duração para o qual a aplicação ocorrerá o erro exceder 60 segundos se vir vários ocorrências do erro num determinado dia, ficheiro ou um pedido de suporte Azure ao selecionar a **Obter suporte** no site de [Suporte do Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Próximos passos
- Se receber um erro diferente, avalie a [mensagem de erro](sql-database-develop-error-messages.md) para pistas sobre a causa.
- Se o problema for persistente, visite as orientações [resolver problemas de ligação comuns para a base de dados do SQL Azure](sql-database-troubleshoot-common-connection-issues.md).
