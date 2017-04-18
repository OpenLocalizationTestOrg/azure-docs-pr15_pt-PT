<properties
    pageTitle="Fazer cópia de segurança com capacidade de esticar bases de dados | Microsoft Azure"
    description="Saiba como criar uma cópia de segurança esticar\-com capacidade de bases de dados."
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
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>Cópia de segurança com capacidade de esticar as bases de dados

Cópias de segurança da base de dados ajudá-lo a recuperar a partir de vários tipos de falhas, erros e catástrofes.  

-   Tem de criar cópia de segurança dos seus esticar\-com capacidade de bases de dados do SQL Server.  

-   Microsoft Azure cópias automaticamente os dados remotos que esticar base de dados tem migrados do SQL Server para Azure.  

>    [AZURE.NOTE] Cópia de segurança é apenas uma parte de um concluída elevada disponibilidade e a solução de continuidade de negócios. Para obter mais informações sobre a disponibilidade de alta, consulte o artigo [Alto de disponibilidade de soluções](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="back-up-your-sql-server-data"></a>Agregar dados do SQL Server  

Efectuar uma cópia do seu esticar\-com capacidade de bases de dados do SQL Server, pode continuar a utilizar os métodos de cópia de segurança do SQL Server que está a utilizar. Para obter mais informações, consulte o artigo [cópia de segurança e restauro de servidor de bases de dados SQL](https://msdn.microsoft.com/library/ms187048.aspx).

Cópias de segurança de uma base de dados do SQL Server com capacidade de esticar só contenham dados locais e dados elegíveis para migração no ponto em tempo quando executa a cópia de segurança. \(Dados elegíveis são dados que ainda não foram migrados, mas que serão migrados para o Azure com base nas definições de migração, as tabelas.\) Este é conhecido como uma cópia de segurança **pouco fundo** e não inclui os dados já migrado para o Azure.  

## <a name="back-up-your-remote-azure-data"></a>Agregar dados Azure remotos   

Microsoft Azure cópias automaticamente os dados remotos que esticar base de dados tem migrados do SQL Server para Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure reduz o risco de perda de dados com cópias de segurança automáticas  
O serviço de base de dados do SQL Server esticar no Azure protege as bases de dados remotos com instantâneos armazenamento automático pelo menos a cada 8 horas. Mantém cada instantâneo durante 7 dias fornecer-lhe um intervalo de pontos de restauro possíveis.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure reduz o risco de perda de dados com geo\-redundância  
Cópias de segurança da base de dados Azure estão armazenadas num geo\-redundantes armazenamento do Windows Azure (RT\-GRS) e, consequentemente, são geo\-redundantes por predefinição. Geo\-armazenamento redundante replica os seus dados para uma região secundária que seja centenas de milhas afastando-o a região principal. Nas regiões principais e secundários, os seus dados são replicados três vezes cada, ao longo de falhas separada domínios e atualização. Isto assegura que os dados estiverem resistentes mesmo no caso de uma falha do regional concluída ou falhas compõe uma das regiões Azure indisponível.

### <a name="stretchRPO"></a>Base de dados esticar reduz o risco de perda de dados para os seus dados Azure por reter linhas migradas temporariamente
Depois de base de dados esticar migra linhas elegíveis do SQL Server para Azure, mantém essas linhas na tabela de transição para um mínimo de 8 horas. Se restaurar uma cópia de segurança da base de dados Azure, esticar base de dados utiliza as linhas guardadas na tabela de transição para reconciliar o SQL Server e as bases de dados Azure.

Depois de restaurar uma cópia de segurança dos seus dados Azure, tem de executar o procedimento armazenado [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) para voltar a ligar a esticar\-com capacidade de base de dados do SQL Server para a base de dados remota Azure. Quando executa **sys.sp_rda_reauthorize_db**, esticar base de dados reconcilia automaticamente o SQL Server e as bases de dados Azure.

Para aumentar o número de horas de dados migrados que esticar base de dados temporariamente mantém na tabela transição, executar o procedimento armazenado [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) e especificar um número de horas maiores do que 8. Para decidir quanto para guardar os dados, considere os seguintes fatores:
-   A frequência de Azure cópias de segurança automáticas (pelo menos a cada 8 horas).
-   O tempo necessário após um problema para reconhecer o problema e decida restaurar uma cópia de segurança.
-   A duração da operação de restaurar Azure.

> [AZURE.NOTE] Aumentar a quantidade de dados que esticar base de dados temporariamente mantém na tabela transição aumenta a quantidade de espaço necessário no SQL Server.

Para verificar o número de horas de dados que esticar base de dados atualmente mantém temporariamente na tabela transição, execute o procedimento armazenado [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Consulte também

[Gerir e resolver problemas esticar base de dados](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Criar cópias de segurança e restauro de bases de dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
