<properties
   pageTitle="Cópias de segurança do armazém de dados SQL | Microsoft Azure"
   description="Saiba mais sobre cópias de segurança do armazém de dados SQL da base de dados incorporados que permitem-lhe restaurar um armazém de dados do SQL Azure para um ponto de restauro ou uma região geográfica diferente."
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lakshmi1812"
   manager="barbkess"
   editor="monicar"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="lakshmir;barbkess"/>

# <a name="sql-data-warehouse-backups"></a>Cópias de segurança do armazém de dados SQL

Armazém de dados SQL oferece as cópias de segurança locais e geográficas como parte das suas capacidades de cópia de segurança de armazém de dados. Estes incluem instantâneos Azure armazenamento Blob e armazenamento geo redundante. Utilize dados armazém as cópias de segurança para restaurar o seu armazém de dados para um ponto de restauro na região principal ou restaurar para uma região geográfica diferente. Este artigo explica as especificidades de cópias de segurança no armazém de dados do SQL.

## <a name="what-is-a-data-warehouse-backup"></a>O que é uma cópia de segurança do armazém de dados?

Uma cópia de segurança do armazém de dados é os dados que pode utilizar para restaurar um armazém de dados para uma hora específica.  Uma vez que armazém de dados SQL é um sistema distribuído, uma cópia de segurança do armazém de dados é composta por muitos ficheiros que estão armazenados no Azure blobs. 

Cópias de segurança da base de dados são uma parte essencial de qualquer estratégia de recuperação de continuidade e falhas empresas porque estes proteger os seus dados a partir de danos acidentais ou eliminação. Para mais informações, consulte o artigo [Descrição geral de continuidade do negócio](../sql-database/sql-database-business-continuity.md).

## <a name="data-redundancy"></a>Redundância de dados

Armazém de dados SQL protege os dados por armazenar os seus dados redundantes localmente armazenamento de Premium do Windows Azure (LRS). Esta funcionalidade de armazenamento do Windows Azure armazena várias cópias síncronas dos dados no Centro de dados local para garantir a proteção de dados transparente se existem falhas localizadas. Redundância de dados assegura que os seus dados podem permanecem problemas de infraestrutura como falhas do disco. Redundância de dados garante continuidade do negócio com uma tolerância a falhas e infraestrutura altamente disponível.

Para saber mais sobre:

- Armazenamento de Premium Azure, consulte o artigo [Introdução ao Azure Premium armazenamento](../storage/storage-premium-storage.md).
- Localmente redundante armazenamento, consulte o artigo [replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md#locally-redundant-storage).


## <a name="azure-storage-blob-snapshots"></a>Instantâneo de armazenamento Blob Azure

Como das vantagens de utilizar o armazenamento do Windows Azure Premium, armazém de dados do SQL utiliza instantâneos Azure armazenamento Blob do armazém de dados localmente uma cópia de segurança. Pode restaurar um armazém de dados para um ponto de restauro instantâneo. Instantâneos iniciar um mínimo de oito em oito horas e estão disponíveis para sete dias.  

Para saber mais sobre:

- Instantâneo de Blobs do Azure, consulte o artigo [criar um instantâneo de BLOBs](../storage/storage-blob-snapshots.md).


## <a name="geo-redundant-backups"></a>Cópias de segurança geo redundantes

A cada 24 horas, armazém de dados SQL armazena armazém dados completo no armazenamento padrão. O armazenamento de dados completa é criado para corresponder a hora da última instantâneo. O armazenamento padrão pertence a uma conta de armazenamento geo redundante com acesso de leitura (GRS RT). A funcionalidade de armazenamento de Azure RT-GRS replica os ficheiros de cópia de segurança para um [Centro de dados pares](../best-practices-availability-paired-regions.md). Esta replicação geo assegura que pode restaurar armazém de dados, caso não consegue aceder os instantâneos na sua região principal. 

Esta funcionalidade está ativada por predefinição. Se não quiser utilizar geo redundantes cópias de segurança, pode optar por. 

>[AZURE.NOTE] Armazenamento Azure, o termo *replicação* refere-se para copiar os ficheiros de uma localização para outra. Do SQL *replicação de bases de dados* que se refere a manter a várias bases de dados secundárias sincronizadas com uma base de dados principal. 

Para saber mais sobre:
- Geo redundantes armazenamento, consulte o artigo [replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md).
- Armazenamento de GRS RT, consulte o artigo [armazenamento geo redundantes acesso de leitura](../storage/storage-redundancy.md#read-access-geo-redundant-storage).

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>Agenda de cópia de segurança e período de retenção do armazém de dados

Armazém de dados SQL cria instantâneos no seu armazém de dados online todas as quatro para oito horas e mantém cada instantâneo durante sete dias. Pode restaurar a base de dados online para um dos pontos de restauro nos últimos sete dias. 

Para ver quando o último instantâneo iniciado, execute esta consulta no seu armazém de dados SQL online. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

Se precisar de manter um instantâneo durante mais de sete dias, pode restaurar um ponto de restauro para um novo armazém de dados. Depois da restaurar está concluída, armazém de dados SQL inicia a criação de instantâneos no armazém de dados nova. Se não efetuar alterações para o novo armazém de dados, os instantâneos manter-se vazia e, consequentemente, o custo de instantâneo for mínimo. Também pode interromper a base de dados para impedir a criação de instantâneos armazém de dados SQL.


### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>O que acontece aos meus retenção de cópia de segurança enquanto está parada meu armazém de dados?

Armazém de dados SQL não criar instantâneos e não expira instantâneos enquanto um armazém de dados está em pausa. A idade instantâneo não irá alterar enquanto o armazenamento de dados está em pausa. Instantâneo de retenção é baseada no número de dias que no armazém de dados não está online, não os dias de calendário.

Por exemplo, se um instantâneo começa 1 de Outubro em 4 pm e o armazenamento de dados está em pausa de Outubro de 3 na 4 pm, o instantâneo é dois dias. Sempre que o armazenamento de dados é fornecido voltar a estar online o instantâneo é dois dias. Se o armazenamento de dados ficar online de Outubro de 5 em 4 pm, o instantâneo dois dias e mantém-se para a mais cinco dias.

Quando o armazenamento de dados surgir novamente online, armazém de dados SQL currículos novos instantâneos e expira instantâneos quando tiverem mais de sete dias de dados.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>Quanto tempo é o período de retenção para um armazém de dados ignorados?
Quando um armazém de dados é ignorado, o armazém de dados e os instantâneos são guardados durante sete dias e, em seguida, removidos. Pode restaurar o armazenamento de dados para qualquer um dos pontos restauro guardados.

> [AZURE.IMPORTANT] Se eliminar uma instância do SQL server lógica, todas as bases de dados que pertencem, para a instância são também eliminadas e não podem ser recuperadas. Não é possível restaurar um servidor eliminado.

## <a name="data-warehouse-backup-costs"></a>Custos de cópia de segurança de armazém de dados

O total de custos para o seu armazém de dados principal e sete dias de instantâneos de Blobs do Azure é arredondado para o mais próximo TB. Por exemplo, se o seu armazém de dados é 1,5 TB e os instantâneos utilizam 100 GB, são faturada para 2 TB de dados a taxas de armazenamento do Windows Azure Premium. 

>[AZURE.NOTE] Cada instantâneo está vazio inicialmente e cresce à medida que efetuar alterações ao armazém de dados principal. Todos os instantâneos aumentam o tamanho como as alterações de armazém de dados. Por isso, os custos de armazenamento para instantâneos crescem em sequência de acordo com a taxa de variação.

Se estiver a utilizar o armazenamento geo redundante, receberá uma taxa de armazenamento em separado. O armazenamento geo redundantes é faturado à taxa padrão de acesso de leitura geograficamente armazenamento redundante (GRS RT).

Para mais informações sobre preços do armazém de dados SQL, consulte o artigo [Preços de armazém de dados do SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="using-database-backups"></a>Utilizar cópias de segurança da base de dados

A utilização principal para cópias de segurança do SQL dados armazém é restaurar o armazenamento de dados para um dos pontos restaurar dentro do período de retenção.  

- Para restaurar um armazém de dados SQL, consulte o artigo [restaurar um armazém de dados SQL](sql-data-warehouse-restore-database-overview.md).


## <a name="related-topics"></a>Tópicos relacionados

### <a name="scenarios"></a>Cenários

- Para obter uma descrição de continuidade do negócio, consulte o artigo [Descrição geral de continuidade do negócio](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

- Para restaurar um armazém de dados, consulte o artigo [restaurar um armazém de dados SQL](sql-data-warehouse-restore-database-overview.md)

<!-- ### Tutorials -->

