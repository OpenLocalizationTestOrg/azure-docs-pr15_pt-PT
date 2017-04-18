<properties
    pageTitle="Restaurar uma única tabela a partir da cópia de segurança da base de dados do Azure SQL | Microsoft Azure"
    description="Saiba como restaurar uma única tabela a partir da cópia de segurança da base de dados do Azure SQL."
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


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Como restaurar uma única tabela a partir de uma cópia de segurança da base de dados do Azure SQL

Poderá encontrar uma situação na qual acidentalmente modificada alguns dados numa base de dados SQL e agora que pretende recuperar a tabela afetada única. Este artigo descreve como restaurar uma única tabela numa base de dados a partir de uma a base de dados SQL [cópias de segurança automáticas](sql-database-automated-backups.md)de.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Passos de preparação: mudar o nome da tabela e restaurar uma cópia da base de dados
1. Identifique a tabela na base de dados Azure SQL que pretende substituir com a cópia restaurada. Utilize o Microsoft SQL Management Studio para mudar o nome da tabela. Por exemplo, mudar o nome da tabela como &lt;nome da tabela&gt;_old.

    **Nota** Para evitar que sejam bloqueados, certifique-se de que não existe nenhuma atividade em execução na tabela que são mudar o nome. Se encontrar problemas, certifique-se de que executar este procedimento durante uma janela de manutenção.

2. Restaure uma cópia de segurança da base de dados para um ponto no momento em que pretende recuperar para através dos passos [Ponto In_Time restaurar](sql-database-recovery-using-backups.md#point-in-time-restore) .

    **Notas**:
    - O nome da base de dados restaurado irá estar no formato DBName + carimbo; Por exemplo, **Adventureworks2012_2016-01-01T22-12Z**. Este passo não irá substituir o nome da base de dados existente no servidor. Esta é uma medida de segurança e destina-tem a permitem-lhe verificar a base de dados restaurado antes de poderem largue a sua base de dados atual e mudar o nome da base de dados restaurado para utilização de produção.
    - Todas as camadas de desempenho de básico Premium automaticamente cópia de segurança abrange pelo serviço, com diversas larguras métricas de retenção de cópia de segurança, consoante a camada:

| Restaurar DB | Camada básica | Camadas padrão | Camadas de Premium |
| :-- | :-- | :-- | :-- |
|  Restaurar ponto no tempo |  Qualquer ponto de restauro dentro de 7 dias|Qualquer ponto de restauro dentro de 35 dias| Qualquer ponto de restauro dentro de 35 dias|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copiar a tabela de base de dados restaurada utilizando a ferramenta de migração de base de dados SQL
1. Transfira e instale o [Assistente de migração de base de dados SQL](https://sqlazuremw.codeplex.com).

2. Abrir o Assistente de migração de base de dados SQL, na página **Selecionar processo** , selecione a **base de dados em analisar/migrar**e, em seguida, clique em **seguinte**.
![Assistente de migração de base de dados do SQL - selecione processo](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Na caixa de diálogo **ligar ao servidor** , aplicam-se as seguintes definições:
 - **Nome do servidor**: instância do seu SQL Azure
 - **Autenticação**: **autenticação do SQL Server**. Introduza as suas credenciais de início de sessão.
 - **Base de dados**: **DB de modelo global (todas as bases de dados de lista)**.
 - **Nota** Por predefinição, o assistente guarda as informações de início de sessão. Se não pretende que seja, selecione **Se esqueça de informações de início de sessão**.
![Passo do assistente - selecione origem - de migração de base de dados do SQL 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Na caixa de diálogo **Selecionar origem** , selecione o nome da base de dados restaurado a partir da secção de **passos de preparação** como origem de e, em seguida, clique em **seguinte**.

    ![Passo do assistente - selecione origem - de migração de base de dados do SQL 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Na caixa de diálogo **Escolher objetos** , selecione a opção **Selecionar objetos de base de dados específico** e, em seguida, selecione o table(or tables) que pretende migrar para o servidor de destino.
![Assistente de migração de base de dados do SQL - selecione objetos](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Na página de **Resumo do Assistente de Script** , clique em **Sim** quando lhe for pedido sobre se estiver pronto para gerar um script SQL. Também tem a opção para guardar o Script TSQL para utilizar posteriormente.
![Assistente de migração de base de dados do SQL - resumo do Assistente de Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Na página de **Resumo de resultados** , clique em **seguinte**.
![Assistente de migração de base de dados do SQL - resumo dos resultados](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Na página **Configuração da ligação ao servidor destino** , clique em **ligar ao servidor**e, em seguida, introduza os detalhes da seguinte forma:
    - **Nome do servidor**: instância de servidor de destino
    - **Autenticação**: **autenticação do SQL Server**. Introduza as suas credenciais de início de sessão.
    - **Base de dados**: **DB de modelo global (todas as bases de dados de lista)**. Esta opção lista todas as bases de dados no servidor de destino.

    ![Assistente de migração de base de dados do SQL - ligação de servidor de destino de configuração](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Clique em **Ligar**, selecione a base de dados de destino que pretende mover a tabela para e, em seguida, clique em **seguinte**. Isto deve concluir a executar o script previamente gerado e deverá ver a tabela recentemente movida copiada para a base de dados de destino.

## <a name="verification-step"></a>Passo de verificação
1. Consulta e testar a tabela para se certificar de que os dados são intactos recentemente copiada. Após a confirmação, pode largar a forma de tabela cujo nome foi mudado secção **passos de preparação** . (por exemplo, &lt;nome da tabela&gt;_old).

## <a name="next-steps"></a>Próximos passos

[Cópias de segurança automáticas de base de dados SQL](sql-database-automated-backups.md)
