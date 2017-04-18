<properties
    pageTitle="Resolver problemas de cópia de segurança e restaurar a base de dados do Azure SQL"
    description="Saiba como recuperar uma base de dados da nuvem de erros e falhas de utilizar cópias de segurança e réplicas numa base de dados do SQL Azure."
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

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>Restaurar uma base de dados para um ponto anterior no tempo, restaurar uma base de dados eliminado ou recuperar a partir de uma falha do Centro de dados

Base de dados SQL mantém réplicas da base de dados para que o pode recuperar a partir do falhas e erros do utilizador. Opções disponíveis dependem a camada de serviços de base de dados e as opções que escolher. Consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md) para obter detalhes e considerações de estrutura.

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>Para restaurar uma base de dados para um ponto anterior no tempo
1.  No [Portal do Azure](https://azure.microsoft.com/), clique em **bases de dados SQL**.
2.  Selecione a base de dados a partir da lista e, em seguida, clique em **Restaurar**.
3.  Escreva um novo nome para a base de dados, escolha a data e hora para restaurar a partir de e, em seguida, clique em **Create.**
4.  Efectue ajustes de aplicação conforme necessário para fazer referência a nova base de dados. Consulte o artigo [recuperar uma base de dados para um ponto de hora](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="to-restore-an-accidentally-deleted-database"></a>Para restaurar uma base de dados eliminado acidentalmente
1.  No [Portal do Azure](https://azure.microsoft.com/), clique em **servidores SQL**.
2.  Selecione o servidor que alojado a base de dados a partir da lista.
3.  No pá Server, desloque-se para baixo e clique em **bases de dados eliminados**.
4.  Selecione a base de dados para restaurar e, em seguida, clique em **Criar**.
5.  Efectue ajustes de aplicação conforme necessário para fazer referência a nova base de dados. Consulte o artigo [recuperar uma base de dados eliminado](sql-database-recovery-using-backups.md#deleted-database-restore).

## <a name="to-recover-from-a-regional-datacenter-outage"></a>Para recuperar a partir de uma falha do Centro de dados regional
Com bases de dados Standard e Premium, se configurou o secundários de replicadas geo, pode recuperar utilizar estes secundários. Isto dá-lhe a capacidade de restaurar uma base de dados com um menos potencial perda de dados. Consulte o artigo [recuperar uma base de dados do Azure SQL com cópias de segurança da base de dados automática](sql-database-disaster-recovery.md) para obter detalhes.

Azure também fornece as cópias de segurança da base de todos os dados numa região diferente (uma geo redundantes cópia de segurança). Pode criar uma nova base de dados a partir destas cópias de segurança, que é denominado Geo restaurar, mas é provável que irá deparar perda de dados se depender este método só por si.

**Para recuperar uma base de dados utilizando geo restaurar:**

- No [Portal do Azure](https://azure.microsoft.com/), clique em **Novo**, clique em **dados e armazenamento**, clique em **Base de dados SQL**e, em seguida, selecione a **cópia de segurança** como a origem de base de dados. Consulte o artigo [recuperar uma base de dados do Azure SQL a partir de uma falha](sql-database-disaster-recovery.md) para obter detalhes.