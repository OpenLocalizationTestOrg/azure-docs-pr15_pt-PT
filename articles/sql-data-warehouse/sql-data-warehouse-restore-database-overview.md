<properties
   pageTitle="Restaurar armazém de dados SQL | Microsoft Azure"
   description="Descrição geral das opções de restauro de base de dados para recuperar uma base de dados no armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/29/2016"
   ms.author="lakshmir;barbkess;sonyama"/>


# <a name="sql-data-warehouse-restore"></a>Restaurar armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Portal][]
- [PowerShell][]
- [RESTO][]

Armazém de dados SQL oferece restaura local e geográficas como parte do seu falhas de armazém dados capacidades de recuperação. Utilizar dados armazém as cópias de segurança para restaurar o seu armazém de dados para um ponto de restauro na região principal ou utilizar geo redundantes cópias de segurança para restaurar uma região geográfica diferente. Este artigo explica os detalhes da restaurar um armazém de dados.

## <a name="what-is-a-data-warehouse-restore"></a>O que é um restauro de armazém de dados?

Um restauro de armazém de dados é um novo armazém de dados que é criado a partir de uma cópia de segurança de um existente ou armazém de dados eliminados. O armazém de dados restaurados recria o armazenamento de dados de cópia de segurança num momento específico. Uma vez que armazém de dados SQL é um sistema distribuído, um restauro de armazém de dados é criado a partir de muitos ficheiros de cópia de segurança que estão armazenados no Azure blobs. 

Restauro de base de dados é uma parte essencial de qualquer estratégia de recuperação de continuidade e falhas empresas porque cria novamente os seus dados depois de danos acidentais ou eliminação.

Para obter mais informações, consulte:

-  [Cópias de segurança do armazém de dados SQL](sql-data-warehouse-backups.md)
-  [Descrição geral de continuidade do negócio](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>Pontos de restauro de armazém de dados

Como das vantagens de utilizar o armazenamento do Windows Azure Premium, armazém de dados SQL utiliza instantâneos Azure armazenamento Blob para fazer cópia de segurança do armazém de dados principal. Cada instantâneo tem um ponto de restauro que representa o tempo limite da iniciado. Para restaurar um armazém de dados, selecione um ponto de restauro e emitir um comando de restaurar.  

Armazém de dados SQL restaura sempre a cópia de segurança para um novo armazém de dados. Pode manter o armazém de dados restaurados e aquele atual ou eliminar uma delas. Se pretender substituir o armazenamento de dados atual com o armazém de dados restaurados, pode alterá-lo.

Se precisar de restaurar um armazém de dados eliminado ou em pausa, pode [criar um bilhetes de suporte](sql-data-warehouse-get-started-create-support-ticket.md). 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>Restaurar geo redundantes

Se estiver a utilizar o armazenamento geo redundantes, pode restaurar o armazenamento de dados para o seu [Centro de dados emparelhado](../best-practices-availability-paired-regions.md) numa região geográfica diferente. O armazenamento de dados é restaurado a partir da última cópia de segurança diária. 

## <a name="restore-timeline"></a>Restaurar a linha cronológica

Pode restaurar uma base de dados para qualquer ponto de restauro disponíveis nos últimos sete dias. Instantâneos iniciar cada quatro para oito horas e estão disponíveis para sete dias. Quando um instantâneo for mais antigo do que sete dias, expire e o seu ponto de restaurar já não está disponível.

## <a name="restore-costs"></a>Restaurar os custos

O encargo de armazenamento para o armazém de dados restaurados é faturado a taxa de juro armazenamento do Windows Azure Premium. 

Se interromper um armazém de dados restaurados, que lhe ser cobrada armazenamento a taxa de armazenamento do Windows Azure Premium. A vantagem de interromper é que não é cobrados para os recursos de informáticos DWU.

Para mais informações sobre preços do armazém de dados SQL, consulte o artigo [Preços de armazém de dados do SQL](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="uses-for-restore"></a>Utilizações para restaurar

A utilização principal para restaurar armazém de dados é recuperar dados depois de dados acidentais perda ou danos.

Também pode utilizar dados armazém restaurar para manter uma cópia de segurança durante mais de sete dias. Assim que a cópia de segurança é restaurada, tiver o armazenamento de dados online e pode colocar em pausa indefinidamente para poupar nos custos de cluster. A base de dados em pausa tarifas de armazenamento a taxa de juro armazenamento do Windows Azure Premium. 

## <a name="related-topics"></a>Tópicos relacionados

### <a name="scenarios"></a>Cenários

- Para obter uma descrição de continuidade do negócio, consulte o artigo [Descrição geral de continuidade do negócio](../sql-database/sql-database-business-continuity.md)


<!-- ### Tasks -->

Para executar uma operação de restauro de armazém de dados, restaure utilizando:

- Azure portal, consulte o artigo [restaurar um armazém de dados através do portal Azure](sql-data-warehouse-restore-database-portal.md)
- Os cmdlets do PowerShell, consulte o artigo [restaurar um armazém de dados utilizando os cmdlets do PowerShell](sql-data-warehouse-restore-database-powershell.md)
- COLOQUE APIs, consulte o artigo [restaurar um armazém de dados utilizando as APIs resto](sql-data-warehouse-restore-database-rest-api.md)

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Descrição geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->
