<properties
   pageTitle="Cópias de segurança da base de dados SQL - automática, geo redundantes | Microsoft Azure" 
   description="Base de dados SQL automaticamente cria uma cópia de segurança da base de dados local em cinco minutos e utiliza armazenamento geo redundantes Azure acesso de leitura (GRS RT) para fornecer redundância de geo. "
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/20/2016"
   ms.author="carlrab;barbkess"/>

<!------------------
This topic is annotated with TEMPLATE guidelines for FEATURE TOPICS.


Metadata guidelines

pageTitle
    60 characters or less. Includes name of the feature - primary benefit. Not the same as H1. Its 60 characters or fewer including all characters between the quotes and the Microsoft Azure site identifier.

description
    115-145 characters. Duplicate of the first sentence in the introduction. This is the abstract of the article that displays under the title when searching in Bing or Google. 

    Example: "SQL Database automatically creates a local database backup every few minutes and uses Azure read-access geo-redundant storage for geo-redundancy."
------------------>

<!----------------

TEMPLATE GUIDELINES for feature topics

The Feature Topic is a one-pager (ok, sometimes longer) that explains a capability of the product or service. It explains what the capability is and characteristics of the capability.  

It is a "learning" topic, not an action topic.

DO explain this:
    • Definition of the feature terminology.  i.e., What is a database backup?
    • Characteristics and capabilities of the feature. (How the feature works)
    • Common uses with links to overview topics that recommend when to use the feature.
    • Reference specifications (Limitations and Restrictions, Permissions, General Remarks, etc.)
    • Next Steps with links to related overviews, features, and tasks.

DON'T explain this:
    • How to steps for using the feature (Tasks)
    • How to solve business problems that incorporate the feature (Overviews)
------------------->

<!------------------
GUIDELINES for the H1 
    
    The H1 should answer the question "What is in this topic?" Write the H1 heading in conversational language and use search key words as much as possible. Since this is a learning topic, make sure the title indicates that and doesn't mislead people to think this will tell them how to do tasks.  
    
    To help people understand this is a learning topic and not an action topic, start the title with "Learn about ... "

    Heading must use an industry standard term. If your feature is a proprietary name like "Elastic database pools", use a synonym. For example:    "Learn about elastic database pools for multi-tenant databases". In this case multi-tenant database is the industry-standard term that will be an anchor for finding the topic.

-------------------->

# <a name="learn-about-sql-database-backups"></a>Saiba mais sobre cópias de segurança da base de dados SQL

<!------------------
    GUIDELINES for introduction
    
    The introduction is 1-2 sentences.  It is optimized for search and sets proper expectations about what to expect in the article. It should contain the top key words that you are using throughout the article.The introduction should be brief and to the point of what the feature is, what it is used for, and what's in the article. 

    If the introduction is short enough, your article can pop to the top in Google Instant Answers.

    In this example:
    
 

Sentence #1 Explains what the article will cover, which is what the feature is or does. This is also the metadata description. 
    SQL Database automatically creates a local database backup every five minutes and uses Azure read-access geo-redundant storage (RA-GRS) to provide geo-redundancy. 

Sentence #2 Explains why I should care about this.  
    Database backups are an essential part of any business continuity and disaster recovery strategy because they protect your data from accidental corruption or deletion.

-------------------->

Base de dados SQL automaticamente cria uma cópia de segurança da base de dados local em poucos minutos e utiliza o Azure armazenamento geo redundantes de acesso de leitura para geo redundância. Cópias de segurança da base de dados são uma parte essencial de qualquer estratégia de recuperação de continuidade e falhas empresas porque estes proteger os seus dados a partir de danos acidentais ou eliminação. 

<!-- This image needs work, so not putting it in right now.

This diagram shows SQL Database running in the US East region. It creates a database backup every five minutes, which it stores locally to Azure Read Access Geo-redundant Storage (RA-GRS). Azure uses geo-replication to copy the database backups to a paired data center in the US West region.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)

-->

<!---------------
GUIDELINES for the first ## H2.

    The first ## describes what the feature encompasses and how it is used. It points to related task articles.
    
    For consistency, being the heading with "What is ... "
----------------->

## <a name="what-is-a-sql-database-backup"></a>O que é uma cópia de segurança da base de dados SQL?  

<!-- 
    Explains what a SQL Database backup is and answers an important question that people want to know.
-->

Uma cópia de segurança da base de dados SQL inclui cópias de segurança da base de dados local e geo redundantes cópias de segurança. Estas cópias de segurança são criadas automaticamente e sem custos acrescidos. Não precisa de fazer nada para torná-las ocorrer.

<!----------------- 
    Explains first component of the backup feature
------------------>

Para cópias de segurança locais, base de dados SQL utiliza tecnologia SQL Server para criar cópias de segurança [completo](https://msdn.microsoft.com/library/ms186289.aspx), [diferença](https://msdn.microsoft.com/library/ms175526.aspx )e [registo de transações](https://msdn.microsoft.com/library/ms191429.aspx) . As cópias de segurança do registo da transação ocorrem em cinco minutos, permite-lhe restaurar um ponto de tempo no mesmo servidor que aloja a base de dados. Quando restaura uma base de dados, o serviço de figuras saída log inteiro, diferença e da transação cópias de segurança precisem de ser restaurada.

<!--------------- 
    Explicit list of what to do with a local backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Utilize uma cópia de segurança da base de dados local para:

- Restaure uma base de dados para um ponto-in-time dentro do período de retenção. Com uma cópia de segurança da base de dados pode restaurar uma base de dados para uma determinada na altura, restaurar uma base de dados eliminado para o tempo que foi eliminado ou restaurar uma base de dados para outra à região geográfica errada. Para executar uma operação de restauro, consulte o artigo [Restaurar uma base de dados a partir de uma cópia de segurança da base de dados](sql-database-recovery-using-backups.md).

- Copie uma base de dados para um servidor SQL na região mesmo ou diferente. A cópia é consistente com a base de dados do SQL atual. Para efetuar uma cópia, consulte o artigo [Copiar de base de dados](sql-database-copy.md).

- Arquive uma cópia de segurança da base de dados para além do período de retenção de cópia de segurança. Para executar um arquivo, [Exportar uma base de dados do SQL para um BACPAC](sql-database-export.md) ficheiro. Em seguida, pode arquivar BACPAC memória a longo prazo e armazená-lo para além do período de retenção. Em alternativa, utilize o BACPAC para transferir uma cópia da base de dados para o SQL Server, quer no local ou numa máquina virtual Azure (VM).

<!----------------- 
    Explains first component of the backup feature
------------------>

Para geo redundantes cópias de segurança, base de dados SQL utiliza a [replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md). Base de dados SQL armazena os ficheiros de cópia de segurança de base de dados local numa conta [Armazenamento Geo redundantes acesso de leitura (GRS RT)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) . Azure replica os ficheiros de cópia de segurança para um [Centro de dados pares](../best-practices-availability-paired-regions.md). 

<!--------------- 
    Explicit list of what to do with a geo-redundant backup. "Use a ..." helps people to scan the topic and find the uses quickly.
---------------->

Utilize uma cópia de segurança geo redundantes:

- Restaure uma base de dados para uma região geográfica diferente, caso não consegue aceder a cópia de segurança da base de dados a partir do seu região de base de dados principal. 

>[AZURE.NOTE] Armazenamento Azure, o termo *replicação* refere-se para copiar os ficheiros de uma localização para outra. Do SQL *replicação de bases de dados* que se refere a manter a várias bases de dados secundárias sincronizadas com uma base de dados principal. 

<!----------------
    The next ## H2's discuss key characteristics of how the feature works. The title is in conversational language and asks the question that will be answered.
------------------->
## <a name="how-much-backup-storage-is-included-at-no-cost"></a>Quantidade de armazenamento cópia de segurança é incluído não custo?

Base de dados SQL fornece até 200% do seu armazenamento de base de dados aprovisionada máximo como uma cópia de segurança armazenamento sem custos adicionais. Por exemplo, se tiver uma instância de DB padrão com um tamanho de DB aprovisionada de 250 GB, terá 500 GB de armazenamento de cópia de segurança sem custos acrescidos. Se a base de dados excede o armazenamento de cópia de segurança fornecido, pode escolher reduzir o período de retenção ao contactar o suporte do Azure. Outra opção é comprar armazenamento extra cópia de segurança que será cobrado à taxa padrão de acesso de leitura geograficamente armazenamento redundante (GRS RT). 

## <a name="how-often-do-backups-happen"></a>Com que frequência acontecer cópias de segurança?

Para cópias de segurança da base de dados local, cópias de segurança da base de dados completa ocorrem semanalmente, cópias de segurança da base de dados diferença ocorrem hora a hora e registo de transações cópias de segurança ocorrem em cinco minutos. A primeira cópia de segurança completa está agendada imediatamente após a criação de uma base de dados. Normalmente, é concluído dentro do 30 minutos, mas pode demorar mais tempo quando estiver a base de dados de dimensão significativa. Por exemplo, a cópia de segurança inicial pode demorar mais tempo numa base de dados restaurado ou uma cópia da base de dados. Após a primeira completa cópia de segurança, todas as cópias de segurança mais são agendadas automaticamente e geridas silenciosamente em segundo plano. A temporização exata de cópias de segurança completa e [diferença](https://msdn.microsoft.com/library/ms175526.aspx) de base de dados é determinada conforme saldos-lo a carga de trabalho geral do sistema. 

Para geo redundantes cópias de segurança, são copiadas cópias de segurança completos e diferença de acordo com a agenda de replicação de armazenamento do Windows Azure.

## <a name="how-long-do-you-keep-my-backups"></a>Quanto tempo que mantém o meu cópias de segurança?

Cada cópia de segurança da base de dados SQL tem um período de retenção baseada na [camada de serviços](sql-database-service-tiers.md) da base de dados. O período de retenção para uma base de dados a:

<!------------------

    Using a list so the information is easy to find when scanning.
------------------->

- Camadas de serviço básico são sete dias.
- Camadas de serviço padrão são de 35 dias.
- Camada de serviços Premium é de 35 dias.


Se alterar a base de dados a partir das camadas de serviço padrão ou Premium para básico, as cópias de segurança são guardadas durante sete dias. Todas as cópias de segurança existentes mais antigas do que sete dias já não estão disponíveis. 

Se atualizar a base de dados da camada de serviço básico para padrão ou Premium, base de dados SQL mantém as cópias de segurança existentes até terem 35 dias. Mantém o novas cópias de segurança medida que estes ocorrem para 35 dias.
 
Se eliminar uma base de dados, base de dados SQL mantém as cópias de segurança da mesma forma que teria para uma base de dados online. Por exemplo, imaginemos que elimina uma base de dados básica com um período de retenção de sete dias. É guardada uma cópia de segurança de quatro dias para três dias mais.

>[AZURE.IMPORTANT]
    Se eliminar o servidor do Azure SQL que aloja bases de dados SQL, todas as bases de dados que pertencem, para o servidor são também eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor eliminado.

<!-------------------
OPTIONAL section
## Best practices 
--------------------->

<!-------------------
OPTIONAL section
## General remarks
--------------------->

<!-------------------
OPTIONAL section
## Limitations and restrictions
--------------------->

<!-------------------
OPTIONAL section
## Metadata
--------------------->

<!-------------------
OPTIONAL section
## Performance
--------------------->

<!-------------------
OPTIONAL section
## Permissions
--------------------->

<!-------------------
OPTIONAL section
## Security
--------------------->

<!-------------------
GUIDELINES for Next Steps

    The last section is Next Steps. Give a next step that would be relevant to the customer after they have learned about the feature and the tasks associated with it.  Perhaps point them to one or two key scenarios that use this feature.

    You don't need to repeat links you have already given them.
--------------------->

## <a name="next-steps"></a>Próximos passos

Cópias de segurança da base de dados são uma parte essencial de qualquer estratégia de recuperação de continuidade e falhas empresas porque estes proteger os seus dados a partir de danos acidentais ou eliminação. Para ver como cópias de segurança de base de dados para uma estratégia de mais amplo, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md).


